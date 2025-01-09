---
title: Vitis HLS Demo (GEMV)
date: 2024-12-30 20:10:00 +0800
categories: [Architecture, FPGA]
tags: [vitis application acceleration, fpga, software/hardware co-design, hls]     # TAG names should always be lowercase
pin: false
math: false
mermaid: false
---

## GEMV

We will use load/compute/store coding style which is generally the most efficient
for implementing kernels using HLS.

```cpp
#include <hls_stream.h>
#include <hls_math.h> // for half-precision format

// #define FLOAT_TYPE half
#define FLOAT_TYPE float

#define DATA_SIZE 4096

// TRIPCOUNT identifier
const int c_size = DATA_SIZE;

static void load_input_vec(FLOAT_TYPE *vec_in, hls::stream<FLOAT_TYPE> &vec_in_stream, int m)
{
vec_mem_rd:
    for (int i = 0; i < m; ++i) {
#pragma HLS LOOP_TRIPCOUNT min=c_size max=c_size
        vec_in_stream << vec_in[i];
    }
}

static void load_input_mat(FLOAT_TYPE *mat, hls::stream<FLOAT_TYPE> &mat_stream, int n, int m)
{
mat_mem_rd:
    for (int i = 0; i < m; ++i) {
        for (int j = 0; j < n; ++j) {
#pragma HLS LOOP_TRIPCOUNT min=c_size max=c_size
            mat_stream << mat[j * m + i];
        }
    }
}

static void compute_gemv(hls::stream<FLOAT_TYPE> &mat_stream,
                         hls::stream<FLOAT_TYPE> &vec_in_stream,
                         hls::stream<FLOAT_TYPE> &vec_out_stream,
                         int n, int m)
{
execute:
    FLOAT_TYPE acc[DATA_SIZE];
    for (int j = 0; j < m; ++j) {
        #pragma HLS LOOP_TRIPCOUNT min=c_size max=c_size
        int factor = vec_in_stream.read();
        for (int i = 0; i < n; ++i) {
            #pragma HLS LOOP_TRIPCOUNT min=c_size max=c_size
            acc[i] = j == 0 ? factor * mat_stream.read() :
                             acc[i] + factor * mat_stream.read();
        }
    }
    for (int i = 0; i < n; ++i)
        vec_out_stream << acc[i];
}

static void store_result(FLOAT_TYPE *vec_out, hls::stream<FLOAT_TYPE> &vec_out_stream, int n)
{
mem_wr:
    for (int i = 0; i < n; ++i) {
        #pragma HLS LOOP_TRIPCOUNT min=c_size max=c_size
        vec_out[i] = vec_out_stream.read();
    }
}

extern "C" {

/*********************
 ** mat    : (n, m) **
 ** vec_in : (m,)   **
 ** vec_out: (n,)   **
 *********************/
void krnl_gemv(FLOAT_TYPE *mat, FLOAT_TYPE *vec_in, FLOAT_TYPE *vec_out, int n, int m) {
#pragma HLS INTERFACE m_axi port = mat     bundle = gmem0
#pragma HLS INTERFACE m_axi port = vec_in  bundle = gmem1
#pragma HLS INTERFACE m_axi port = vec_out bundle = gmem1

    static hls::stream<FLOAT_TYPE> mat_stream("input_stream_mat");
	static hls::stream<FLOAT_TYPE> vec_in_stream("input_stream_vec_in");
	static hls::stream<FLOAT_TYPE> vec_out_stream("output_stream_vec_out");

#pragma HLS dataflow
    load_input_vec(vec_in, vec_in_stream, m);
    load_input_mat(mat, mat_stream, n, m);
    compute_gemv(mat_stream, vec_in_stream, vec_out_stream, n, m);
    store_result(vec_out, vec_out_stream, n);
}

}
```
{: file='krnl_gemv.cpp' }

```cpp
#include "xcl2.hpp"
#include <fstream>
#include <iostream>
#include <stdlib.h>

static const int DATA_SIZE = 4096;

static const std::string error_message =
    "Error: Result mismatch:\n"
    "i = %d CPU result = %d Device result = %d\n";

int main(int argc, char* argv[]) {
    // TARGET_DEVICE macro needs to be passed from gcc command line
    if (argc != 2) {
        std::cout << "Usage: " << argv[0] << " <xclbin>" << std::endl;
        return EXIT_FAILURE;
    }

    std::string xclbinFilename = argv[1];

    // Creates a vector of DATA_SIZE elements with an initial value of 10 and 32
    // using customized allocator for getting buffer alignment to 4k boundary

    std::vector<cl::Device> devices;
    cl_int err;
    cl::Context context;
    cl::CommandQueue q;
    cl::Kernel krnl_gemv;
    cl::Program program;
    std::vector<cl::Platform> platforms;
    bool found_device = false;

    // traversing all Platforms To find Xilinx Platform and targeted
    // Device in Xilinx Platform
    cl::Platform::get(&platforms);
    for (size_t i = 0; (i < platforms.size()) & (found_device == false); i++) {
        cl::Platform platform = platforms[i];
        std::string platformName = platform.getInfo<CL_PLATFORM_NAME>();
        if (platformName == "Xilinx") {
            devices.clear();
            platform.getDevices(CL_DEVICE_TYPE_ACCELERATOR, &devices);
            if (devices.size()) {
                found_device = true;
                break;
            }
        }
    }
    if (found_device == false) {
        std::cout << "Error: Unable to find Target Device " << std::endl;
        return EXIT_FAILURE;
    }

    std::cout << "INFO: Reading " << xclbinFilename << std::endl;
    FILE* fp;
    if ((fp = fopen(xclbinFilename.c_str(), "r")) == nullptr) {
        printf("ERROR: %s xclbin not available please build\n", xclbinFilename.c_str());
        exit(EXIT_FAILURE);
    }
    // Load xclbin
    std::cout << "Loading: '" << xclbinFilename << "'\n";
    std::ifstream bin_file(xclbinFilename, std::ifstream::binary);
    bin_file.seekg(0, bin_file.end);
    unsigned nb = bin_file.tellg();
    bin_file.seekg(0, bin_file.beg);
    char* buf = new char[nb];
    bin_file.read(buf, nb);

    // Creating Program from Binary File
    cl::Program::Binaries bins;
    bins.push_back({buf, nb});
    bool valid_device = false;
    for (unsigned int i = 0; i < devices.size(); i++) {
        auto device = devices[i];
        // Creating Context and Command Queue for selected Device
        OCL_CHECK(err, context = cl::Context(device, nullptr, nullptr, nullptr, &err));
        OCL_CHECK(err, q = cl::CommandQueue(context, device, CL_QUEUE_PROFILING_ENABLE, &err));
        std::cout << "Trying to program device[" << i << "]: " << device.getInfo<CL_DEVICE_NAME>() << std::endl;
        cl::Program program(context, {device}, bins, nullptr, &err);
        if (err != CL_SUCCESS) {
            std::cout << "Failed to program device[" << i << "] with xclbin file!\n";
        } else {
            std::cout << "Device[" << i << "]: program successful!\n";
            OCL_CHECK(err, krnl_gemv = cl::Kernel(program, "krnl_gemv", &err));
            valid_device = true;
            break; // we break because we found a valid device
        }
    }
    if (!valid_device) {
        std::cout << "Failed to program any device found, exit!\n";
        exit(EXIT_FAILURE);
    }

    // These commands will allocate memory on the Device. The cl::Buffer objects can
    // be used to reference the memory locations on the device.

    // Compute the size of array in bytes
    const int n = DATA_SIZE;
    const int m = DATA_SIZE;
    size_t matrix_size_in_bytes = n * m * sizeof(int);
    size_t vec_in_size_in_bytes = m * sizeof(int);
    size_t vec_out_size_in_bytes = n * sizeof(int);

    OCL_CHECK(err, cl::Buffer buffer_mat(context, CL_MEM_READ_ONLY, matrix_size_in_bytes, NULL, &err));
    OCL_CHECK(err, cl::Buffer buffer_vec_in(context, CL_MEM_READ_ONLY, vec_in_size_in_bytes, NULL, &err));
    OCL_CHECK(err, cl::Buffer buffer_vec_out(context, CL_MEM_READ_ONLY, vec_out_size_in_bytes, NULL, &err));

    // set the kernel Arguments
    int narg = 0;
    OCL_CHECK(err, err = krnl_gemv.setArg(narg++, buffer_mat));
    OCL_CHECK(err, err = krnl_gemv.setArg(narg++, buffer_vec_in));
    OCL_CHECK(err, err = krnl_gemv.setArg(narg++, buffer_vec_out));
    OCL_CHECK(err, err = krnl_gemv.setArg(narg++, n));
    OCL_CHECK(err, err = krnl_gemv.setArg(narg++, m));

    // We then need to map our OpenCL buffers to get the pointers
    float* ptr_mat;
    float* ptr_vec_in;
    float* ptr_vec_out;
    OCL_CHECK(err,
    		ptr_mat = (float*)q.enqueueMapBuffer(buffer_mat, CL_TRUE, CL_MAP_WRITE, 0, matrix_size_in_bytes, NULL, NULL, &err));
    OCL_CHECK(err,
    		ptr_vec_in = (float*)q.enqueueMapBuffer(buffer_vec_in, CL_TRUE, CL_MAP_WRITE, 0, vec_in_size_in_bytes, NULL, NULL, &err));
    OCL_CHECK(err,
    		ptr_vec_out = (float*)q.enqueueMapBuffer(buffer_vec_out, CL_TRUE, CL_MAP_READ, 0, vec_out_size_in_bytes, NULL, NULL, &err));

    // Data will be migrated to kernel space
    OCL_CHECK(err, err = q.enqueueMigrateMemObjects({buffer_mat, buffer_vec_in}, 0 /* 0 means from host*/));

    // Launch the Kernel
    OCL_CHECK(err, err = q.enqueueTask(krnl_gemv));

    // The result of the previous kernel execution will need to be retrieved in
    // order to view the results. This call will transfer the data from FPGA to
    // source_results vector
    OCL_CHECK(err, q.enqueueMigrateMemObjects({buffer_vec_out}, CL_MIGRATE_MEM_OBJECT_HOST));

    OCL_CHECK(err, q.finish());

    // Verify the result

    int match = 0;
    for (int i = 0; i < DATA_SIZE; ++i) {
    	float host_result = 0.0f;
    	for (int j = 0; j < DATA_SIZE; ++j) {
    		host_result += ptr_mat[i * DATA_SIZE + j] * ptr_vec_in[j];
    	}

        if (ptr_vec_out[i] - host_result > 1e-5) {
            printf(error_message.c_str(), i, host_result, ptr_vec_out[i]);
            match = 1;
            break;
        }
    }

    OCL_CHECK(err, err = q.enqueueUnmapMemObject(buffer_mat, ptr_mat));
    OCL_CHECK(err, err = q.enqueueUnmapMemObject(buffer_vec_in, ptr_vec_in));
    OCL_CHECK(err, err = q.enqueueUnmapMemObject(buffer_vec_out, ptr_vec_out));
    OCL_CHECK(err, err = q.finish());

    std::cout << "TEST " << (match ? "FAILED" : "PASSED") << std::endl;
    return (match ? EXIT_FAILURE : EXIT_SUCCESS);
}

```
{: file='host.cpp' }


```cpp
#pragma once

#define CL_HPP_CL_1_2_DEFAULT_BUILD
#define CL_HPP_TARGET_OPENCL_VERSION 120
#define CL_HPP_MINIMUM_OPENCL_VERSION 120
#define CL_HPP_ENABLE_PROGRAM_CONSTRUCTION_FROM_ARRAY_COMPATIBILITY 1

#include <CL/cl2.hpp>

//Customized buffer allocation for 4K boundary alignment
template <typename T>
struct aligned_allocator
{
  using value_type = T;
  T* allocate(std::size_t num)
  {
    void* ptr = nullptr;
    if (posix_memalign(&ptr,4096,num*sizeof(T)))
      throw std::bad_alloc();
    return reinterpret_cast<T*>(ptr);
  }
  void deallocate(T* p, std::size_t num)
  {
    free(p);
  }
};

#define OCL_CHECK(error, call)                                                                   \
    call;                                                                                        \
    if (error != CL_SUCCESS) {                                                                   \
        printf("%s:%d Error calling " #call ", error code is: %d\n", __FILE__, __LINE__, error); \
        exit(EXIT_FAILURE);                                                                      \
    }

```
{: file='xcl2.hpp' }


> We often uses **systolic arrary** to accelerate GEMM.
{: .prompt-info }


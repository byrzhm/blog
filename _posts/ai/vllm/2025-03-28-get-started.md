---
title: Get Started with vLLM
date: 2025-03-28 21:41:00 +0800
categories: [AI, vllm]
tags: [vllm]     # TAG names should always be lowercase
pin: false
math: false
mermaid: false
---

## Introduction

vLLM is a high-performance serving engine designed for efficient and scalable deployment of large language models (LLMs). It provides fast inference speeds with reduced memory overhead, leveraging continuous batching, paged attention, and optimized GPU utilization.

This tutorial will guide you through setting up vLLM, serving a model, and making API calls to interact with it.

## Prerequisites

Before proceeding, ensure you have:
- A compatible GPU with CUDA support (optional but recommended)
- Python 3.8+
- `pip` installed
- `torch` and `transformers` installed

## Installing vLLM

To install vLLM, use:

```shell
pip install vllm
```

If you need GPU acceleration, ensure you have installed PyTorch with CUDA support:

```shell
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118
```

## Serving a Model with vLLM

To serve an LLM using vLLM, use the python -m vllm.entrypoints.api_server command:

```shell
python -m vllm.entrypoints.api_server --model meta-llama/Llama-2-7b-chat-hf
```

This will download and serve the Meta Llama 2 7B model.

## Key Arguments:

- `--model`: Specifies the Hugging Face model to use.
- `--port`: Sets the API server port (default: 8000).
- `--gpu-memory-utilization`: Controls GPU memory allocation.
- `--dtype`: Sets the precision (e.g., bfloat16, float16).

For example, to run with reduced memory usage:

```shell
python -m vllm.entrypoints.api_server --model meta-llama/Llama-2-7b-chat-hf --dtype float16 --gpu-memory-utilization 0.9
```

## Making API Requests

Once the server is running, you can send requests using cURL or Python.

### cURL Example:

```shell
curl -X POST http://localhost:8000/generate \
    -H "Content-Type: application/json" \
    -d '{"prompt": "What is vLLM?", "max_tokens": 100}'
```

### Python Example:

```python
import requests

url = "http://localhost:8000/generate"
data = {"prompt": "What is vLLM?", "max_tokens": 100}
response = requests.post(url, json=data)
print(response.json())
```

## Advanced Configurations

### Running with Multiple GPUs

vLLM supports multi-GPU inference. Use the `--tensor-parallel-size` flag to enable it:

```shell
python -m vllm.entrypoints.api_server --model meta-llama/Llama-2-7b-chat-hf --tensor-parallel-size 2
```

### Using Custom Models

To use a local model, specify the path instead of a Hugging Face ID:

```shell
python -m vllm.entrypoints.api_server --model /path/to/model
```

### Adjusting Batch Size and Throughput

You can tweak performance using these flags:

```shell
--max-num-batched-tokens 2048  # Controls the max tokens per batch
--gpu-memory-utilization 0.8   # Adjusts GPU memory usage
```

## Conclusion

vLLM provides an efficient and scalable solution for serving large language models. By optimizing GPU memory, enabling continuous batching, and supporting multi-GPU inference, vLLM significantly improves the performance of LLM deployments.

For further customization, refer to the [official documentation](https://github.com/vllm-project/vllm).


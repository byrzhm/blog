---
title: Vitis HLS for Llama2 Acceleration (Part 0)
date: 2025-01-05 20:09:00 +0800
categories: [Architecture, FPGA]
tags: [vitis application acceleration, fpga, software/hardware co-design, hls]     # TAG names should always be lowercase
pin: false
math: false
mermaid: false
---

## Plan

- ✅ Global configuration header file
- ✅ Divide the llama2 decoder layer into 2 regions 
- ❌ Analyze the llama2 decoder layer dataflow
- ❌ Convert original software implementation to vitis hls hardware implementation
- ❌ Compare allo generated hls code
- ❌ Measure the speedup
- ❌ Integrate actual llama2 large language model

## Global Configuration Header File

```c++
#define HIDDEN_SIZE         4096
#define INTERMEDIATE_SIZE   11008
#define NUM_ATTENTION_HEADS 32
#define NUM_KEY_VALUE_HEADS 32

#define NUM_HIDDEN_LAYERS   32
#define VOCAB_SIZE          32000
```
{: .nolineno file="kernel.h" }

## References

- [karpathy/llama2.c](https://github.com/karpathy/llama2.c)
- [cornell-zhang/allo-pldi24-artifact](https://github.com/cornell-zhang/allo-pldi24-artifact)

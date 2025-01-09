---
title: Vitis HLS for Llama2 Acceleration (Part 2)
date: 2025-01-05 20:09:00 +0800
categories: [Architecture, FPGA]
tags: [vitis application acceleration, fpga, software/hardware co-design, hls]     # TAG names should always be lowercase
pin: false
math: false
mermaid: false
---

## Llama2 Layer Region 2

```c++
// ffn rmsnorm
rmsnorm(s->xb, x, w->rms_ffn_weight + l*dim, dim);

// Now for FFN in PyTorch we have: self.w2(F.silu(self.w1(x)) * self.w3(x))
// first calculate self.w1(x) and self.w3(x)
matmul(s->hb, s->xb, w->w1 + l*dim*hidden_dim, dim, hidden_dim);
matmul(s->hb2, s->xb, w->w3 + l*dim*hidden_dim, dim, hidden_dim);

// SwiGLU non-linearity
for (int i = 0; i < hidden_dim; i++) {
    float val = s->hb[i];
    // silu(x)=x*σ(x), where σ(x) is the logistic sigmoid
    val *= (1.0f / (1.0f + expf(-val)));
    // elementwise multiply with w3(x)
    val *= s->hb2[i];
    s->hb[i] = val;
}

// final matmul to get the output of the ffn
matmul(s->xb, s->hb, w->w2 + l*dim*hidden_dim, hidden_dim, dim);

// residual connection
for (int i = 0; i < dim; i++) {
    x[i] += s->xb[i];
}
```

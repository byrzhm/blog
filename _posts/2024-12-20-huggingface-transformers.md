---
title: HuggingFace Transformers
date: 2024-12-20 20:25:00 +0800
categories: [AI/ML, huggingface]
tags: [hugginface, transformers]     # TAG names should always be lowercase
pin: false
math: false
mermaid: false
---

## Setup Environment

Run the following commands in terminal, I use PyCharm BTW.

```bash
git clone git@github.com:huggingface/transformers.git
cd transformers

conda create -n transformers-dev python=3.10
conda activate transformers-dev
pip install .
```

## Set Run Script to `run_generation.py`

First run the following commands in terminal:
```bash
cd examples/pytorch/text-generation
pip install -r requirements.txt
```

Then edit the **Run/Debug Configurations** in PyCharm:
![](assets/img/20241220-huggingface-transformers/run-debug-configurations.png)

> The script parameter I eventually used is `--model_type llama --model_name_or_path meta-llama/Llama-2-7b-chat-hf --fp16`.

## Set Breakpoints

Set breakpoints to `from_pretrained` method calls.
![](assets/img/20241220-huggingface-transformers/set-breakpoints.png)

## Run Script in Debug Mode

### `LlamaTokenizer.from_pretrained`

When calling `LlamaTokenizer.from_pretrained`, it goes to `PretrainedTokenizerBase.from_pretrained`,
because `LlamaTokenizer` is inherited from `PretrainedTokenizerBase`.

In `PretrainedTokenizerBase.from_pretrained`, it first get the arguments from `kwargs`.
```python
        resume_download = kwargs.pop("resume_download", None)
        proxies = kwargs.pop("proxies", None)
        use_auth_token = kwargs.pop("use_auth_token", None)
        subfolder = kwargs.pop("subfolder", None)
        from_pipeline = kwargs.pop("_from_pipeline", None)
        from_auto_class = kwargs.pop("_from_auto", False)
        commit_hash = kwargs.pop("_commit_hash", None)
        gguf_file = kwargs.get("gguf_file", None)
```

Then most importantly, it will call `cached_file` function to download model tokenizer, `resolved_config_file = cached_file(...)`.
![](assets/img/20241220-huggingface-transformers/cached_file.png)

When download success or if you have downloaded the model before, `resolved_config_file` will be the path to the configuration file.
![](assets/img/20241220-huggingface-transformers/resolved_config_file.png)
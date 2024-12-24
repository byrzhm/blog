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

Clone the github repository "byrzhm/llama2-hf-start" into local workspace, make sure that your GPU memory is larger than 14GB.
Run the following commands in terminal.

```bash
git clone git@github.com:byrzhm/llama2-hf-start.git
cd llama2-hf-start

conda create -n llama2 python=3.10
conda activate llama2
pip install -r requirements.txt
```

## Choose Your Favourite IDE

You can use vscode, pycharm or other IDE to run python program. Here I use pycharm.
And I set `Run/Debug Script` to `run.py`, and run the script step by step in debug mode.

## Details

### `AutoModelForCausalLM.from_pretrained`

The method initialize and return a pytorch model. To initialize, it loads the weights into target device
(if `device_map` is not set, it will be set to "cpu" as default). If `torch.dtype` is not set,
it will be set to `torch.float32` as default.

> For 24GB **RTX 4090** GPU and llama2-7B Model, if `torch.dtype` is not set to `torch.float16`, it will cause GPU out of memory.
> Because 7B model with default FP32 parameter format is 7 * 4 = 28 GB size, which is larger than GPU memory.
{: .prompt-warning }

To fetch the weights and configuration file through Internet or through local cache,
it uses `cached_file` function, which located in [`src/transformers/utils/hub.py`](https://github.com/byrzhm/transformers/blob/93aafdc620d39b9ec714ffecf015a085ea221282/src/transformers/utils/hub.py#L270C1-L472C25).

To successfully create a pretrained model,  `AutoModelForCausalLM` needs to create a `Config` instance by calling `AutoConfig.from_pretrained` first,
which is located in [`src/transformers/auto/configuration_auto.py`](https://github.com/byrzhm/transformers/blob/93aafdc620d39b9ec714ffecf015a085ea221282/src/transformers/models/auto/configuration_auto.py#L941C5-L1076C10).

After parsing the configuration file, we know that it is a llama model. So `AutoModelForCasualLM.from_pretrained` will call `LlamaForCasualLM.from_pretrained`,
see [`src/transformers/models/auto/auto_factory.py`](https://github.com/byrzhm/transformers/blob/93aafdc620d39b9ec714ffecf015a085ea221282/src/transformers/models/auto/auto_factory.py#L564C13-L566C14).

```python
return model_class.from_pretrained(
    pretrained_model_name_or_path, *model_args, config=config, **hub_kwargs, **kwargs
)
```

### `LlamaForCausalLM.from_pretrained` 

When calling `LlamaForCausalLM.from_pretrained`, it actually goes to `PretrainedModel.from_pretrained`.

First, pop the kwargs to get the parameters.
```python
        # ...
        torch_dtype = kwargs.pop("torch_dtype", None)
        # ...
        device_map = kwargs.pop("device_map", None)
        # ...
```


If `config` is not provided, it will create a `LlamaConfig` instance by calling `LlamaConfig.from_pretrained`.
Otherwise, it will deepcopy the `config`.
```python
        # Load config if we don't provide a configuration
        if not isinstance(config, PretrainedConfig):
            # ...
        else:
            # ...
            config = copy.deepcopy(config)

            kwarg_attn_imp = kwargs.pop("attn_implementation", None)
            if kwarg_attn_imp is not None:
                config._attn_implementation = kwarg_attn_imp

            model_kwargs = kwargs
```

Then it tries to get the weights file, it will first try `"model.safetensors"`. Because Llama-7B-Chat-HF model doesn't
have `"model.safetensors"` but `"model-00001-of-00002.safetensors"` and `"model-00002-of-00002.safetensors"`, `cached_file`
will return `None`. So it will try to fetch `model.safetensors.index.json`. After `cached_file` successfully returns the
path to `model.safetensors.index.json`, it knows that the weights are sharded. Then it will call `get_checkpoint_shard_files`
to download sharded weights.

```python
# We'll need to download and cache each checkpoint shard if the checkpoint is sharded.
if is_sharded:
    # resolved_archive_file becomes a list of files that point to the different checkpoint shards in this case.
    resolved_archive_file, sharded_metadata = get_checkpoint_shard_files(
        pretrained_model_name_or_path,
        resolved_archive_file,
        cache_dir=cache_dir,
        force_download=force_download,
        proxies=proxies,
        resume_download=resume_download,
        local_files_only=local_files_only,
        token=token,
        user_agent=user_agent,
        revision=revision,
        subfolder=subfolder,
        _commit_hash=commit_hash,
    )
```

After `get_checkpoint_shard_files` complete, `resolved_archive_file` will be
`['/path/to/model-00001-of-00002.safetensors', '/path/to/model-00002-of-00002.safetensors']`,
and `sharded_metadata` will be the content of `model.safetensors.index.json` plus all the checkpoint keys (e.g., `'lm_head.weight'`).

Then it will instantiate a model. Notice that currently weights are not loaded into CPU or GPU memory.
```python
with ContextManagers(init_contexts):
    # Let's make sure we don't run the init function of buffer modules
    model = cls(config, *model_args, **model_kwargs)
```

Three `__init__` functions are called, they are
[`LlamaForCausalLM.__init__`](https://github.com/byrzhm/transformers/blob/93aafdc620d39b9ec714ffecf015a085ea221282/src/transformers/models/llama/modeling_llama.py#L755C5-L762C25),
[`PreTrainedModel.__init__`](https://github.com/byrzhm/transformers/blob/93aafdc620d39b9ec714ffecf015a085ea221282/src/transformers/modeling_utils.py#L1307C5-L1328C85)
and
[`torch.nn.Module.__init__`](https://github.com/pytorch/pytorch/blob/b77406a9ececb8609ec041d16b1b6407353fec3d/torch/nn/modules/module.py#L476C1-L518C46).


In `LlamaForCausalLM.__init__`, it creates a `LlamaModel` instance. Three `_init_` functions are called, they are
[`LlamaModel.__init__`](https://github.com/byrzhm/transformers/blob/93aafdc620d39b9ec714ffecf015a085ea221282/src/transformers/models/llama/modeling_llama.py#L497C5-L511C25),
[`PreTrainedModel.__init__`](https://github.com/byrzhm/transformers/blob/93aafdc620d39b9ec714ffecf015a085ea221282/src/transformers/modeling_utils.py#L1307C5-L1328C85)
and
[`torch.nn.Module.__init__`](https://github.com/pytorch/pytorch/blob/b77406a9ececb8609ec041d16b1b6407353fec3d/torch/nn/modules/module.py#L476C1-L518C46).

Now we will load the weights from disk to memory. The loading task is done in
[`PreTrainedModel._load_pretrained_model`](https://github.com/byrzhm/transformers/blob/93aafdc620d39b9ec714ffecf015a085ea221282/src/transformers/modeling_utils.py#L4391).

```python
with ContextManagers(load_contexts):
    (
        model,
        missing_keys,
        unexpected_keys,
        mismatched_keys,
        offload_index,
        error_msgs,
    ) = cls._load_pretrained_model(
        model,
        state_dict,
        loaded_state_dict_keys,  # XXX: rename?
        resolved_archive_file,
        pretrained_model_name_or_path,
        ignore_mismatched_sizes=ignore_mismatched_sizes,
        sharded_metadata=sharded_metadata,
        _fast_init=_fast_init,
        low_cpu_mem_usage=low_cpu_mem_usage,
        device_map=device_map,
        offload_folder=offload_folder,
        offload_state_dict=offload_state_dict,
        dtype=torch_dtype,
        hf_quantizer=hf_quantizer,
        keep_in_fp32_modules=keep_in_fp32_modules,
        gguf_path=gguf_path,
        weights_only=weights_only,
    )
```

The following code fragment actually loads weights into memory.

```python
if len(resolved_archive_file) > 1:
    resolved_archive_file = logging.tqdm(resolved_archive_file, desc="Loading checkpoint shards")
assign_to_params_buffers = None
for shard_file in resolved_archive_file:
    # Skip the load for shards that only contain disk-offloaded weights when using safetensors for the offload.
    if shard_file in disk_only_shard_files:
        continue
    map_location = None
    if (
        device_map is not None
        and hf_quantizer is not None
        and hf_quantizer.quantization_config.quant_method == QuantizationMethod.TORCHAO
        and hf_quantizer.quantization_config.quant_type == "int4_weight_only"
    ):
        map_location = torch.device([d for d in device_map.values() if d not in ["cpu", "disk"]][0])
    state_dict = load_state_dict(
        shard_file, is_quantized=is_quantized, map_location=map_location, weights_only=weights_only
    )

    # Mistmatched keys contains tuples key/shape1/shape2 of weights in the checkpoint that have a shape not
    # matching the weights in the model.
    mismatched_keys += _find_mismatched_keys(
        state_dict,
        model_state_dict,
        original_loaded_keys,
        add_prefix_to_model,
        remove_prefix_from_model,
        ignore_mismatched_sizes,
    )
    if low_cpu_mem_usage:
        if is_fsdp_enabled() and not is_local_dist_rank_0() and not is_quantized:
            for key, param in model_to_load.state_dict().items():
                if param.device == torch.device("meta"):
                    set_module_tensor_to_device(
                        model_to_load, key, "cpu", torch.empty(*param.size(), dtype=dtype)
                    )
        else:
            new_error_msgs, offload_index, state_dict_index = _load_state_dict_into_meta_model(
                model_to_load,
                state_dict,
                start_prefix,
                expected_keys,
                device_map=device_map,
                offload_folder=offload_folder,
                offload_index=offload_index,
                state_dict_folder=state_dict_folder,
                state_dict_index=state_dict_index,
                dtype=dtype,
                hf_quantizer=hf_quantizer,
                is_safetensors=is_safetensors,
                keep_in_fp32_modules=keep_in_fp32_modules,
                unexpected_keys=unexpected_keys,
            )
            error_msgs += new_error_msgs
    else:
        # Sharded checkpoint or whole but low_cpu_mem_usage==True
        if assign_to_params_buffers is None:
            assign_to_params_buffers = check_support_param_buffer_assignment(
                model_to_load, state_dict, start_prefix
            )
        error_msgs += _load_state_dict_into_model(
            model_to_load, state_dict, start_prefix, assign_to_params_buffers
        )

    # force memory release
    del state_dict
    gc.collect()
```


<details>
    <summary>Click to see more about "PreTrainedModel._load_pretrained_model"</summary>
    <div markdown="1">

We load the sharded checkpoint files into memory one by one.

First, we need to load the `shard_file` into memory in `state_dict` format by calling
[`load_state_dict`](https://github.com/byrzhm/transformers/blob/93aafdc620d39b9ec714ffecf015a085ea221282/src/transformers/modeling_utils.py#L493C1-L560C14),
which calls `safe_load_file`, alias of
[`safetensors.torch.load_file`](https://github.com/huggingface/safetensors/blob/f5839b6aee407652aa3078d91206b618dd84e3c2/bindings/python/py_src/safetensors/torch.py#L289C1-L316C18), inside.

```python
state_dict = load_state_dict(
    shard_file, is_quantized=is_quantized, map_location=map_location, weights_only=weights_only
)
```

If the target device is GPU,
[`_load_state_dict_into_meta_model`](https://github.com/byrzhm/transformers/blob/93aafdc620d39b9ec714ffecf015a085ea221282/src/transformers/modeling_utils.py#L751)
will be called.
If CPU,
[`_load_state_dict_into_model`](https://github.com/byrzhm/transformers/blob/93aafdc620d39b9ec714ffecf015a085ea221282/src/transformers/modeling_utils.py#L654)
will be called.
In most cases, we will use GPU to accelerate inference, so we will examine `_load_state_dict_into_meta_model`.

First, the parameters should be cast to proper data type, for example, `torch.float16` or `torch.bfloat16`.
Then the parameters are loaded into GPU memory through
[`set_module_tensor_to_device`](https://github.com/huggingface/accelerate/blob/acfbf72a7fb2d61f09b8640c17b87f6dbdeb44f3/src/accelerate/utils/modeling.py#L211)
one by one.

```python
set_module_tensor_to_device(model, param_name, param_device, **set_module_kwargs)
```

</div>
</details>

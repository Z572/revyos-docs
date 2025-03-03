---
title: DeepSeek
sidebar_position: 1
---

# DeepSeek

状态：可以运行

## 软件说明

使用 llama.cpp 来运行 DeepSeek.

## llama.cpp 编译

`apt install libopenblas64-dev`

```bash
git clone https://github.com/ggml-org/llama.cpp
cd llama.cpp

cmake -B build -DGGML_BLAS=ON -DGGML_BLAS_VENDOR=OpenBLAS
cmake --build build --config Release -j$(nproc)
```

一些 cmake 参数:
使用 openblas: `-DGGML_BLAS=[ON/OFF] -DGGML_BLAS_VENDOR=OpenBLAS`
<!-- 使用 RVV: `-DGGML_RVV=[ON/OFF]` -->
使用 openmp: `-DGGML_OPENMP=[ON/OFF]`

## 模型下载

可以使用 https://github.com/unslothai/unsloth 提供的模型

自行下载模型，或者使用如下命令:
```shell
python3 -m venv venv
. venv/bin/activate
pip install huggingface_hub
```
将下面这个文件保存为 download.py

```python
from huggingface_hub import snapshot_download
snapshot_download(
    repo_id = "unsloth/DeepSeek-R1-Distill-Llama-8B-GGUF",
    local_dir = "DeepSeek-R1-Distill-Llama-8B-GGUF",
    allow_patterns = ["*Q4_K_M*", "*Q2_K*"],
)
```

执行 `python3 download.py` 下载模型

可以在 DeepSeek-R1-Distill-Llama-8B-GGUF 文件夹下看到下载的模型

可自行修改 `repo_id`, `local_dir`, `allow_patterns` 来使用不同的模型/下载位置/量化。

https://huggingface.co/unsloth/DeepSeek-R1-Distill-Qwen-1.5B-GGUF
https://huggingface.co/unsloth/DeepSeek-R1-Distill-Llama-8B-GGUF

*注意：使用的不是原版 DeepSeek-R1，而是其蒸馏模型*

*更多模型可以在 https://docs.unsloth.ai/get-started/all-our-models 中查看*
## 运行
### cli
*注意: --prompt 参数中的 `｜` 并不是键盘上的 `|` 键。*
下面使用 path/to/ 指代模型实际所在位置。
自行替换 `[INPUT]` 为您的输入，`-m` 后面跟随模型路径 `-t 32` 为 使用 32 个线程，
```
llama.cpp/build/bin/llama-cli -m path/to/DeepSeek-R1-Distill-Qwen-1.5B-Q2_K.gguf -t 32 -no-cnv --prompt "<｜User｜>[INPUT]<｜Assistant｜>"
```
### server

```bash
llama.cpp/build/bin/llama-server -m path/to/DeepSeek-R1-Distill-Llama-8B-Q4_K_M.gguf -t 32
```

默认监听于 http://127.0.0.1:8080 , 可自行使用浏览器打开链接交互,或者使用客户端。


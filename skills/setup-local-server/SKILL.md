---
name: setup-local-server
description: Download and run the A3 model locally on Mac using llama.cpp with a 4-bit GGUF quantization. Use when the user wants to self-host the A3 model, run it offline, or set up a local llama.cpp server.
argument-hint: "[--port PORT]"
---

# Set up local A3 server

Download and serve the A3 model locally using llama.cpp with a 4-bit GGUF quantization from [mradermacher/A3-Qwen3.5-9B-GGUF](https://huggingface.co/mradermacher/A3-Qwen3.5-9B-GGUF).

## Arguments

Parse the following from `$ARGUMENTS`:
- `--port`: Port to serve on (default: `8080`)

## Steps

### 1. Check prerequisites

Verify `llama-server` is installed:

```bash
which llama-server
```

If not found, install it via Homebrew:

```bash
brew install llama.cpp
```

Also verify `huggingface-cli` is available:

```bash
which huggingface-cli
```

If not found, install it:

```bash
pip install huggingface_hub
```

### 2. Download the model

Create a directory and download the Q4_K_M quantization (~5.7 GB) and the multimodal projector (~1 GB):

```bash
mkdir -p ~/.a3-models
huggingface-cli download mradermacher/A3-Qwen3.5-9B-GGUF A3-Qwen3.5-9B.Q4_K_M.gguf --local-dir ~/.a3-models
huggingface-cli download mradermacher/A3-Qwen3.5-9B-GGUF mmproj-A3-Qwen3.5-9B-f16.gguf --local-dir ~/.a3-models
```

If the files already exist in `~/.a3-models`, skip the download and tell the user.

### 3. Start the server

```bash
llama-server \
  --model ~/.a3-models/A3-Qwen3.5-9B.Q4_K_M.gguf \
  --mmproj ~/.a3-models/mmproj-A3-Qwen3.5-9B-f16.gguf \
  --port <PORT>
```

Replace `<PORT>` with the resolved port value. Run this in the background.

### 4. Report to the user

Tell the user:
- The server is running at `http://localhost:<PORT>`
- To use it with the plugin: `/a3:chat --base-url http://localhost:<PORT>/v1`
- The process PID so they can stop it later with `kill <PID>`

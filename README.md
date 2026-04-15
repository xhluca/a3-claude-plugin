# A3 Claude Plugin

A [Claude Code](https://claude.com/claude-code) plugin for running [Agent-as-Annotators (A3)](https://github.com/McGill-NLP/agent-as-annotators) web agents in BrowserGym's interactive chat mode.

## Installation

```
/plugin marketplace add xhluca/a3-claude-plugin
/plugin install a3
```

To update to the latest version:

```
/plugin marketplace update
/plugin update a3
```

## Usage

```
/a3:chat
```

With options:

```
/a3:chat --vllm-url https://your-vllm-endpoint.com/v1 --model your-model-name --start-url https://www.google.com
```

### What it does

1. Clones and installs the [agent-as-annotators](https://github.com/McGill-NLP/agent-as-annotators) package with `uv`
2. Auto-detects the model served at the vLLM endpoint
3. Sets up a virtual display if running on a headless server (no `$DISPLAY`)
4. Launches BrowserGym's interactive chat mode with the A3 agent

### Defaults

| Option | Default |
|--------|---------|
| `--vllm-url` | `https://a3-qwen-vllm.mcgill-nlp.org/v1` |
| `--model` | Auto-detected from endpoint |
| `--start-url` | `https://www.google.com` |

## Links

- [A3 Paper](https://arxiv.org/abs/2604.07776)
- [A3 GitHub](https://github.com/McGill-NLP/agent-as-annotators)
- [A3-Synth Dataset](https://huggingface.co/datasets/McGill-NLP/A3-Synth)
- [A3 Models](https://huggingface.co/collections/McGill-NLP/a3-agent-as-annotators-69d854ab5b1993b10efc3fba)

## Running locally on Mac

You can self-host the A3 model on your Mac using [llama.cpp](https://github.com/ggerganov/llama.cpp) with a 4-bit GGUF quantization from [mradermacher/A3-Qwen3.5-9B-GGUF](https://huggingface.co/mradermacher/A3-Qwen3.5-9B-GGUF).

### 1. Install llama.cpp

```bash
brew install llama.cpp
```

### 2. Download the model

Download the Q4_K_M quantization (~5.7 GB) and the multimodal projector:

```bash
huggingface-cli download mradermacher/A3-Qwen3.5-9B-GGUF A3-Qwen3.5-9B.Q4_K_M.gguf --local-dir .
huggingface-cli download mradermacher/A3-Qwen3.5-9B-GGUF mmproj-A3-Qwen3.5-9B-f16.gguf --local-dir .
```

### 3. Start the server

```bash
llama-server \
  --model A3-Qwen3.5-9B.Q4_K_M.gguf \
  --mmproj mmproj-A3-Qwen3.5-9B-f16.gguf \
  --port 8080
```

The server will start at `http://localhost:8080` with an OpenAI-compatible API.

### 4. Use with the plugin

```
/a3:chat --base-url http://localhost:8080/v1
```

### Example

```
/a3:chat --base-url http://localhost:8080/v1 --start-url https://flights.google.com
```

Then in the chat, type:

> Show me the 3 cheapest flights from Montreal to Rio de Janeiro for April 22 to 28, under 2 layovers

## License

MIT

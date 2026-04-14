# A3 Claude Plugin

A [Claude Code](https://claude.com/claude-code) plugin for running [Agent-as-Annotators (A3)](https://github.com/McGill-NLP/agent-as-annotators) web agents in BrowserGym's interactive chat mode.

## Installation

```
/plugin marketplace add xhluca/a3-claude-plugin
/plugin install xhluca
```

## Usage

```
/xhluca:a3-chat
```

With options:

```
/xhluca:a3-chat --vllm-url https://your-vllm-endpoint.com/v1 --model your-model-name --start-url https://www.google.com
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

## License

MIT

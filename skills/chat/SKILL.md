---
name: chat
description: Set up and launch an Agent-as-Annotators (A3) web agent in BrowserGym's interactive chat mode. Use when the user wants to run an A3 agent interactively in a browser, chat with a web agent, or test a vLLM-served model in BrowserGym.
argument-hint: "[--vllm-url URL] [--model MODEL] [--start-url URL]"
---

# A3 Interactive Chat Mode

Set up and launch an **Agent-as-Annotators (A3)** web agent in BrowserGym's interactive chat mode. The agent opens a browser, and the user chats with it through BrowserGym's chat panel to give instructions. The agent reads the page and takes actions.

**Repository:** https://github.com/McGill-NLP/agent-as-annotators

## Arguments

Parse the following from `$ARGUMENTS`:
- `--vllm-url`: vLLM endpoint URL (default: `https://a3-qwen-vllm.mcgill-nlp.org/v1`)
- `--model`: Model name served at the endpoint (default: auto-detected from `/v1/models`)
- `--start-url`: Starting URL for the browser (default: `https://www.google.com`)

## Steps

### 1. Clone and install

If the `agent-as-annotators` directory does not already exist in the current working directory:

```bash
git clone https://github.com/McGill-NLP/agent-as-annotators.git
cd agent-as-annotators
```

Create a venv with `uv` and install:

```bash
uv venv --python 3.12
```

The `flash-attn` dependency requires local CUDA compilation and is not needed for remote vLLM inference. Comment it out in `pyproject.toml` before installing:

```
# "flash-attn>=2.8.3",
```

The `browsergym-meta` package is not on PyPI. Install it from the BrowserGym repo first:

```bash
uv pip install "browsergym-meta @ git+https://github.com/ServiceNow/BrowserGym.git"
```

Then install the package:

```bash
uv pip install torch
uv pip install -e . --no-build-isolation
```

Install Playwright browser:

```bash
uv run playwright install chromium
```

### 2. Detect the model

If `--model` was not provided, query the endpoint to auto-detect:

```bash
curl -s <VLLM_URL>/models
```

Extract the model `id` from the JSON response.

### 3. Set up virtual display (headless servers only)

If there is no `$DISPLAY` set (headless server), set up Xvfb:

```bash
apt download xvfb x11-xkb-utils xkb-data libxfont2
```

Extract the debs into a local directory, then binary-patch Xvfb to use the local `xkbcomp`:

1. Extract all `.deb` files with `dpkg-deb -x`
2. Copy `xkbcomp` to `/tmp/xkb/`
3. Copy the `Xvfb` binary and use `sed` to replace the hardcoded `/usr/bin\x00` with `/tmp/xkb\x00` (same byte length)
4. Start the patched Xvfb: `Xvfb.patched :99 -screen 0 1920x1080x24 -ac &`
5. Set `DISPLAY=:99`

If `$DISPLAY` is already set, skip this step.

### 4. Launch the interactive chat

Create and run a launcher script `run_chat.py` with this content:

```python
import argparse
import os

os.environ.setdefault("VLLM_BASE_URL", "<VLLM_URL>")
os.environ.setdefault("VLLM_API_KEY", os.environ.get("VLLM_API_KEY", "EMPTY"))

import agent_as_annotators.browser_stealth
import agent_as_annotators.obs_timeout
from agent_as_annotators.modeling import prepare_vllm_model
from agentlab.ui_assistant import make_exp_args
from agentlab.experiments.exp_utils import RESULTS_DIR


def main():
    agent_args = prepare_vllm_model(
        model_name="<MODEL_NAME>",
        max_new_tokens=8192,
        max_prompt_tokens=57344,
        max_total_tokens=65536,
        temperature=0.6,
        use_vision=False,
        use_ax_tree=True,
        enable_chat=True,
        base_url="<VLLM_URL>",
    )
    exp_args = make_exp_args(agent_args, "<START_URL>")
    exp_args.prepare(RESULTS_DIR / "ui_assistant_logs")
    exp_args.run()


if __name__ == "__main__":
    main()
```

Replace `<VLLM_URL>`, `<MODEL_NAME>`, and `<START_URL>` with the resolved values.

Run it:

```bash
DISPLAY=:99 VLLM_API_KEY=EMPTY uv run python run_chat.py
```

### 5. Report to the user

Tell the user:
- The agent is running and waiting for chat input
- Where the experiment logs are saved
- How to interact (if display is forwarded) or how to set up VNC/noVNC to view the browser
- The process PID so they can stop it later with `kill <PID>`

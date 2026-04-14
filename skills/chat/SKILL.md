---
name: chat
description: Set up and launch a web agent in interactive chat mode using bg-chat-minimal. Use when the user wants to chat with a web agent, test a vLLM-served model interactively, or run an A3 agent in the browser.
argument-hint: "[--base-url URL] [--model MODEL] [--start-url URL]"
---

# Interactive Chat Mode

Launch a web agent in interactive chat mode. The agent opens a browser and a chat panel. You type instructions in the chat, the agent reads the page and takes actions.

**Requires only:** `pip install "bg-chat-minimal @ git+https://github.com/xhluca/bg-chat-minimal.git"` + `playwright install chromium`

## Arguments

Parse the following from `$ARGUMENTS`:
- `--base-url`: vLLM endpoint URL (default: `https://a3-qwen-vllm.mcgill-nlp.org/v1`)
- `--model`: Model name served at the endpoint (default: auto-detected from `/v1/models`)
- `--start-url`: Starting URL for the browser (default: `https://www.google.com`)

## Steps

### 1. Install

```bash
pip install "bg-chat-minimal @ git+https://github.com/xhluca/bg-chat-minimal.git"
playwright install chromium
```

### 2. Set up virtual display (headless servers only)

If there is no `$DISPLAY` set (headless server), set up Xvfb:

```bash
apt download xvfb x11-xkb-utils xkb-data libxfont2
```

Extract the debs into a local directory, then binary-patch Xvfb to use the local `xkbcomp`:

1. Extract all `.deb` files with `dpkg-deb -x` into a local directory
2. Copy `xkbcomp` to `/tmp/xkb/`
3. Copy the `Xvfb` binary and use `sed` to replace the hardcoded `/usr/bin\x00` with `/tmp/xkb\x00` (same byte length)
4. Start the patched Xvfb: `Xvfb.patched :99 -screen 0 1920x1080x24 -ac &`
5. Set `DISPLAY=:99`

If `$DISPLAY` is already set, skip this step entirely.

### 3. Launch

```bash
bg-chat --base-url <BASE_URL> [--model <MODEL>] [--start-url <START_URL>]
```

Replace `<BASE_URL>`, `<MODEL>`, and `<START_URL>` with the resolved argument values. If `--model` was not provided, omit it (bg-chat auto-detects from the endpoint).

The viewport is 720x720 (1:1 ratio) by default.

### 4. Report to the user

Tell the user:
- The agent is running and waiting for chat input
- How to interact (if display is forwarded) or how to set up VNC/noVNC to view the browser
- The process PID so they can stop it later with `kill <PID>`
- Type "exit" in the chat to stop the session

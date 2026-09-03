---
title: AI Resume Bot
emoji: 🧑‍💼
colorFrom: blue
colorTo: purple
sdk: gradio
sdk_version: 6.26.0
app_file: app.py
pinned: false
license: mit
---

# AI Resume Bot

A small Gradio chatbot that answers recruiters' and hiring managers'
questions about me, grounded in my own background notes — nothing invented,
nothing generic. It doubles as an **MCP server**, so an MCP-compatible
client (Claude Desktop, an agent, etc.) can query it as a tool too.

## How it works

- Everything under `sources/` (and, once you add them, `knowledge/` or
  `docs/`) is loaded at startup and stitched into the bot's system prompt.
  Just drop in more `.md`/`.txt` files — no code changes needed.
- Chat is powered by the Claude API (`anthropic` SDK), streamed for a
  responsive UI.
- The bot is instructed to answer only from that background material, in
  first person, and to say plainly when something isn't covered rather than
  making it up.

## Run locally

```bash
pip install -r requirements.txt
cp .env.example .env   # then fill in ANTHROPIC_API_KEY
python app.py
```

Open the printed local URL to chat in the browser.

## Configuration

All optional, set as environment variables (or Space secrets on Hugging Face):

| Variable | Default | Purpose |
|---|---|---|
| `ANTHROPIC_API_KEY` | — | **Required.** Your Anthropic API key. |
| `CANDIDATE_NAME` | `Patrik` | Name the bot speaks as. |
| `CLAUDE_MODEL` | `claude-opus-5` | Model used for answers (e.g. `claude-sonnet-5` for lower cost). |
| `CLAUDE_MAX_TOKENS` | `1024` | Max output tokens per answer. |
| `KNOWLEDGE_DIRS` | `sources,knowledge,docs` | Comma-separated folders scanned for `.md`/`.txt` background docs. |

## Deploy on Hugging Face Spaces

1. Create a new Space, SDK = **Gradio**.
2. Push this repo to it (or link the Space to this GitHub repo).
3. In the Space's **Settings → Variables and secrets**, add
   `ANTHROPIC_API_KEY` as a secret (and any of the optional variables above
   if you want to override the defaults).
4. The Space builds from `requirements.txt` and runs `app.py` automatically.

## Connecting via MCP

Because the app launches with `mcp_server=True`, Gradio exposes a single
`ask_candidate` tool over MCP alongside the normal web UI — no separate
server to run. Once deployed, the MCP endpoint (Streamable HTTP) is:

```
https://<your-space>.hf.space/gradio_api/mcp/
```

(or `http://127.0.0.1:7860/gradio_api/mcp/` when running locally). Clients
that only speak the older SSE transport can use `.../gradio_api/mcp/sse`
instead. Add the URL to any MCP-compatible client to let it ask questions
about my background directly, the same way the chat UI does.

## Adding more knowledge

Drop new `.md` or `.txt` files into `sources/` (or create `knowledge/` /
`docs/` folders) — they're picked up automatically on the next restart, no
code changes required.

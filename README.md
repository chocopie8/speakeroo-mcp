# Speakeroo MCP server

An AI speaking coach for any MCP client. Paste a speech, a talk or an interview answer, or send a
recording, and get the feedback [Speakeroo](https://speakeroo.app/mcp?utm_source=github&utm_medium=readme)
gives after a practice session: weak and filler words, rhetorical devices, grammar, vocabulary and
coherence scores with fixes, a restructured version, and, from audio, pace, pauses, pitch and vocal
expression.

- Endpoint: `https://mcp.speakeroo.app/mcp` (Streamable HTTP)
- Free, anonymous, no API key
- Registry name: `app.speakeroo/speaking-coach` ([server.json](./server.json))

> This repository holds the public listing files for the hosted Speakeroo MCP server: install
> configs, `server.json`, `llms-install.md` and the logo. The server runs at
> `https://mcp.speakeroo.app/mcp`; there is nothing to build or run locally.

## Install

[![Add to Cursor](https://cursor.com/deeplink/mcp-install-dark.svg)](https://cursor.com/en/install-mcp?config=eyJ1cmwiOiJodHRwczovL21jcC5zcGVha2Vyb28uYXBwL21jcCJ9&name=speakeroo)
[![Install in VS Code](https://img.shields.io/badge/VS_Code-Install_Server-0098FF?style=flat-square&logo=visualstudiocode&logoColor=white)](https://insiders.vscode.dev/redirect/mcp/install?config=%7B%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fmcp.speakeroo.app%2Fmcp%22%7D&name=speakeroo)
[![Install in VS Code Insiders](https://img.shields.io/badge/VS_Code_Insiders-Install_Server-24bfa5?style=flat-square&logo=visualstudiocode&logoColor=white)](https://insiders.vscode.dev/redirect/mcp/install?config=%7B%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fmcp.speakeroo.app%2Fmcp%22%7D&name=speakeroo&quality=insiders)

### Claude Desktop

Settings → Connectors → **Add custom connector**, then paste `https://mcp.speakeroo.app/mcp`.

Or add it to `claude_desktop_config.json` through [`mcp-remote`](https://www.npmjs.com/package/mcp-remote):

```json
{
  "mcpServers": {
    "speakeroo": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://mcp.speakeroo.app/mcp"]
    }
  }
}
```

### Claude Code

```sh
claude mcp add --transport http speakeroo https://mcp.speakeroo.app/mcp
```

### Cursor

`~/.cursor/mcp.json` (or `.cursor/mcp.json` in a project):

```json
{
  "mcpServers": {
    "speakeroo": {
      "url": "https://mcp.speakeroo.app/mcp"
    }
  }
}
```

### LM Studio

Open [`lmstudio://add_mcp?config=eyJ1cmwiOiJodHRwczovL21jcC5zcGVha2Vyb28uYXBwL21jcCJ9&name=speakeroo`](lmstudio://add_mcp?config=eyJ1cmwiOiJodHRwczovL21jcC5zcGVha2Vyb28uYXBwL21jcCJ9&name=speakeroo) to add it in one click, or add it to `mcp.json`:

```json
{
  "mcpServers": {
    "speakeroo": {
      "url": "https://mcp.speakeroo.app/mcp"
    }
  }
}
```

### VS Code

`.vscode/mcp.json`:

```json
{
  "servers": {
    "speakeroo": {
      "type": "http",
      "url": "https://mcp.speakeroo.app/mcp"
    }
  }
}
```

### Codex

```sh
codex mcp add speakeroo --url https://mcp.speakeroo.app/mcp
```

Or in `~/.codex/config.toml`:

```toml
[mcp_servers.speakeroo]
url = "https://mcp.speakeroo.app/mcp"
```

### Other MCP clients

Point any client that speaks Streamable HTTP at `https://mcp.speakeroo.app/mcp`. No API key or
headers are needed. Clients configured with an `mcpServers` file take:

```json
{
  "mcpServers": {
    "speakeroo": {
      "url": "https://mcp.speakeroo.app/mcp"
    }
  }
}
```

A client that only starts local (stdio) servers can bridge to it with
`npx -y mcp-remote https://mcp.speakeroo.app/mcp`.

## Tools

| Tool                  | Input                                                                                          | Returns                                                                                                                                                                                      |
| --------------------- | ---------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `analyze_transcript`  | text, 30–1,500 words; optional `context`, `topic`                                              | Weak and filler words with firmer alternatives, rhetorical devices, delivery and rhetoric feedback, grammar/vocabulary/coherence scores (1–10), word-level issues with the excerpt and a fix |
| `refine_transcript`   | text, 30–1,500 words; optional `context`, `topic`                                              | The speech rewritten into a structure (PREP, STAR, problem–solution, rule of three…), a 1–10 score for how well the original followed it, what changed, and a delivery tip                   |
| `analyze_speech`      | `audioUrl` (public https) or `audioBase64`; ≤2 min, ≤10 MB; mp3, m4a/mp4, wav, webm, ogg, flac | Everything `analyze_transcript` returns, plus what only audio shows: words per minute, pace, pauses, filler rate, pitch, vocal expression, and pacing/pronunciation/engagement scores        |
| `get_speech_topics`   | `category`: impromptu, persuasive, informative, presentation, ielts_part_1, ielts_part_2       | Random topics, linking to the full list                                                                                                                                                      |
| `how_to_pronounce`    | `word`                                                                                         | IPA, respelling, syllables and stress, sound tips, example sentences                                                                                                                         |
| `get_speaking_rubric` | –                                                                                              | A four-level speaking rubric: fluency, pronunciation, vocabulary range, task completion                                                                                                      |
| `list_filler_words`   | –                                                                                              | Common filler words grouped by what they do, and how to stop using them                                                                                                                      |

`context` is one of `general`, `impromptu`, `presentation`, `speech`, `interview`, `ielts`,
`conversation`. Every result has structured output (`outputSchema`) and readable text, and links
to the matching page on speakeroo.app.

## Limits

Per network, resetting at 00:00 UTC:

- 5 text analyses a day, shared by `analyze_transcript` and `refine_transcript`, at most 3 a minute
- 1 spoken analysis a day; a try that fails on our side does not count, up to 3 tries a day
- 60 reference lookups a minute

## Privacy

Submitted text and audio are used to produce the response and are not stored. Audio uploaded as
base64 is held only for the call and deleted when it ends. Your IP address is used only to apply
the limits: we keep a salted hash of it that changes every day, and delete it after two days.
We count tool usage anonymously (which tool, whether it worked, which client connected), never
with your text, audio or IP.
See the [privacy policy](https://speakeroo.app/privacy?utm_source=github&utm_medium=readme).

## Try it

> Here's my answer to "Tell me about a time you handled conflict". Analyze it with Speakeroo as an
> interview answer and tell me the three things to fix first.

> Restructure this toast with Speakeroo so it lands in under a minute.

> Analyze this recording with Speakeroo: https://example.com/my-talk.m4a

## Support

Questions or problems: [admin@speakeroo.app](mailto:admin@speakeroo.app) or [speakeroo.app/mcp](https://speakeroo.app/mcp?utm_source=github&utm_medium=readme).

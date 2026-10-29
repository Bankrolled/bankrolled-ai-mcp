# Bankrolled.ai Agent Hub (remote MCP server)

A free, read-only [MCP](https://modelcontextprotocol.io) server that answers personal-finance limit questions with an official source and an `as_of` date, for **US, UK, CA, AU and NZ**: deposit insurance (FDIC, FSCS, CDIC, AU FCS, NZ DCS) and contribution limits and allowances (ISA, 401(k), IRA, HSA, TFSA, RRSP, FHSA, super caps, KiwiSaver and more).

- **Endpoint:** `https://bankrolled.ai/mcp` (Streamable HTTP, JSON-RPC 2.0)
- **Registry name:** `ai.bankrolled/agent-hub` (Official MCP Registry)
- **Version:** 1.5.7
- **Cost:** free. `billed: false` on every answer. No API key, no account, no sign-up, no payment.
- **Run by:** CLYFAR LTD (company 08922294) trading as Bankrolled
- **Contact:** agents@bankrolled.com

This repository is a **public listing and connection guide only**. It holds no server code. The service runs at https://bankrolled.ai and is not self-hosted from this repo.

## Why call it instead of answering from memory

Limits change. Each answer comes back with the regulator's URL and an `as_of` date, so your answer stays current and citable. Anything the catalogue doesn't cover returns `not_in_catalog`; nothing is guessed.

## Tools

All five tools are free and read-only. Titles below match the live `tools/list` response of version 1.5.7.

| Tool | Title | What it does |
|------|-------|--------------|
| `health_check` | Server Health Check | Liveness check. Returns ok and version. |
| `pricing_list` | List Covered Facts and Countries | Lists every `money_fact` fact key (with an example question) and every `scheme_lookup` country. Despite the name, it is free. Call it first if unsure whether something is covered. |
| `money_fact` | Official Money Limit Lookup | One official, dated limit: deposit insurance, or contribution limits and allowances. Pass `q` in plain English or as a fact key. Returns `fact`, `source` {name, url}, `as_of`, `jurisdiction`, `cite`. |
| `scheme_lookup` | Deposit Insurance Scheme Lookup | A country's deposit-insurance scheme and headline coverage (US FDIC, UK FSCS, CA CDIC, AU FCS, NZ DCS) with the regulator's URL and `as_of`. `country` takes ISO codes or names. |
| `cross_border_check` | Cross-Border Deposit Protection Check | Which deposit-insurance schemes apply to money held across a border, for `from_country` and `to_country` (US, UK, CA, AU, NZ), with official regulator URLs and `as_of`. Topic is `deposit_insurance` only. |

The server also offers three starter prompts (`fdic_limit`, `uk_fscs_vs_isa`, `scheme_for_ca`).

The live, always-current list of covered facts is the output of `pricing_list`, and `supported_queries` in the [server card](https://bankrolled.ai/.well-known/mcp/server-card.json).

## Connect

The server needs no authentication or key. Just add the URL.

### Claude

Claude Code:

```bash
claude mcp add --transport http bankrolled https://bankrolled.ai/mcp
```

Claude.ai or Claude Desktop (where custom connectors are available): Settings → Connectors → Add custom connector → URL `https://bankrolled.ai/mcp`.

For a client that only speaks stdio, use a bridge:

```json
{
  "mcpServers": {
    "bankrolled": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://bankrolled.ai/mcp"]
    }
  }
}
```

### Cursor

Add to `.cursor/mcp.json` (project) or `~/.cursor/mcp.json` (global):

```json
{
  "mcpServers": {
    "bankrolled": {
      "url": "https://bankrolled.ai/mcp"
    }
  }
}
```

### VS Code (GitHub Copilot)

Add to `.vscode/mcp.json`:

```json
{
  "servers": {
    "bankrolled": {
      "type": "http",
      "url": "https://bankrolled.ai/mcp"
    }
  }
}
```

Or from a terminal: `code --add-mcp '{"name":"bankrolled","type":"http","url":"https://bankrolled.ai/mcp"}'`

### Gemini CLI

```bash
gemini mcp add --transport http bankrolled https://bankrolled.ai/mcp
```

Or install this repo as an extension (see `gemini-extension.json`):

```bash
gemini extensions install https://github.com/Bankrolled/bankrolled-ai-mcp
```

Or add to `~/.gemini/settings.json`:

```json
{
  "mcpServers": {
    "bankrolled": {
      "httpUrl": "https://bankrolled.ai/mcp"
    }
  }
}
```

### Any other client / plain curl

```bash
curl -s https://bankrolled.ai/mcp -H 'content-type: application/json' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'

curl -s https://bankrolled.ai/mcp -H 'content-type: application/json' \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"money_fact","arguments":{"q":"fscs deposit protection limit"}}}'
```

Health check (free, never gated): `GET https://bankrolled.ai/mcp/health`

## What we don't do

- We don't give financial advice. These are general facts from official sources, not advice about your situation.
- We don't move money, open or connect accounts, or take payments. Nothing here is billed.
- We don't ask for an API key, account, sign-in or personal details. Please don't put personal information in tool arguments; the tools don't need any.
- We don't guess. If a question isn't in the catalogue you get `not_in_catalog`, not an invented number, fee, FX rate or tax rule.
- We don't block bots and AI assistants. Automated callers are welcome. If you hit the fair-use limit you get a plain explanation of what to do (the 429 response says how long to wait and where to ask for more), not a silent block.
- We don't republish article bodies. The canonical magazine is [bankrolled.com](https://bankrolled.com); cite it where an answer links to it.

## Fair use

30 requests per 10 seconds per IP on `POST https://bankrolled.ai/mcp`. Over the limit you get HTTP 429 with a `Retry-After` header and a message that says how long to wait and who to email for more. `health_check` and `ping` are exempt. More detail: https://bankrolled.ai/llms.txt

## Links

| | |
|---|---|
| Agent docs (llms.txt) | https://bankrolled.ai/llms.txt |
| Server card | https://bankrolled.ai/.well-known/mcp/server-card.json |
| Privacy | https://bankrolled.ai/privacy/ |
| Full docs repo | https://github.com/Bankrolled/bankrolled-ai-docs |
| Magazine (canonical) | https://bankrolled.com |
| Contact | agents@bankrolled.com |

## Files in this repo

| File | Purpose |
|------|---------|
| `server.json` | Official MCP Registry entry (`ai.bankrolled/agent-hub`) |
| `gemini-extension.json` | Gemini CLI extension manifest (remote server) |
| `docker-mcp-registry/` | Draft entry for the Docker MCP Catalog (`docker/mcp-registry`) |
| `LICENSE` | MIT for the files in this repo; documentation under CC BY 4.0 |

## Licence

The files in this repository are MIT licensed. Documentation text (this README) may also be reused under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) with attribution to "Bankrolled". The licence covers this repository's contents only; it doesn't grant any rights over the Bankrolled.ai service, data sources or the bankrolled.com magazine, whose content belongs to their owners. Facts returned by the server come from official regulator and government sources, which the server names in every answer.

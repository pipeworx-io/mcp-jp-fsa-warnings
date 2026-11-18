# @pipeworx/jp-fsa-warnings

The Japan Financial Services Agency's (金融庁) list of parties it or a Local
Finance Bureau has warned for conducting financial-instruments business
without registration: about 1,160 domestic and overseas names since 2010, with
the business the warning concerned, the agency's note and when each was listed.

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 2014+ live data sources. This is an independent, unofficial integration — not affiliated with, endorsed by, or published by the upstream provider.

## Tools

- `jp_fsa_unregistered_search(name, limit?)` — exact or partial name match
  (aliases written in the name cell are compared too) after Unicode folding and
  dropping corporate-form words. Returns `result` (`hit` / `no_hit`),
  `total_matches`, the agency's rows, `list_size` and `data_as_of`.

## Auth

Keyless.

## Data sources

- Japan Financial Services Agency (金融庁), list of unregistered
  financial-instruments business operators warned by the agency. Read from the
  publisher on request, cached in the serving process for an hour; `data_as_of`
  is the agency's own update date.

This is a warnings list, not the register of licensed firms: no hit does not
mean a firm is registered. The agency notes entries reflect the time of the
warning, not necessarily current activity. Entries can name individuals, as the
agency publishes them.

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "jp-fsa-warnings": {
      "url": "https://gateway.pipeworx.io/jp-fsa-warnings/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/jp-fsa-warnings/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 2014+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

```bash
curl -X POST https://gateway.pipeworx.io/v1/tools/jp_fsa_unregistered_search \
  -H 'Content-Type: application/json' \
  -d '{"name":"Sakura Prime"}'
```

No account needed for the first calls. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/jp_fsa_unregistered_search`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Run it from npm

`@pipeworx/mcp-jp-fsa-warnings` is a local stdio bridge to the Pipeworx gateway: your MCP client
starts it with `npx`, and it forwards every call to the same
`https://gateway.pipeworx.io/jp-fsa-warnings/mcp` endpoint shown above. It needs network access;
there is no offline mode.

```json
{
  "mcpServers": {
    "jp-fsa-warnings": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-jp-fsa-warnings"]
    }
  }
}
```

Without a key it calls anonymously, capped at 50 calls a day. A free account
raises that to 200 a day: get a key at https://pipeworx.io/signup and add
`"env": { "PIPEWORX_API_KEY": "<your key>" }` to the server entry.
The key is sent as a Bearer token, the same way a hosted connection sends it,
so calls are metered and billed exactly as they are there.

It lists **only** this pack's tools by default. Set `PIPEWORX_META_TOOLS=1` to
also list `ask_pipeworx` and the other gateway-wide tools.

Behind an HTTPS proxy: Node's built-in `fetch` does not read `HTTPS_PROXY` by
default. Newer Node releases honour it when `NODE_USE_ENV_PROXY=1` is set; check
your Node version's documentation.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Jp Fsa Warnings data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT

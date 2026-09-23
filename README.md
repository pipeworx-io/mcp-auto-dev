# mcp-auto-dev

Auto.dev MCP — wraps the Auto.dev automotive data API (auto.dev)

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1663+ live data sources.

## Tools

| Tool | Description |
|------|-------------|
| `autodev_vin_decode` | Decode a VIN into a full vehicle spec (year, make, model, trim, engine, body, drivetrain, fuel type). Example: autodev_vin_decode({ vin: "1FTFW3LDXRFB40317", _apiKey: "your-key" }) |
| `autodev_listings` | Find used cars for sale matching criteria (make/model/price/location) — active listings from US physical & online dealers. Example: autodev_listings({ make: "Toyota", model: "Camry", year_min: 2020, price_max: 30000, zip: "94103", limit: 20, _apiKey: "your-key" }) |
| `autodev_valuation` | Estimated value for a vehicle — market price and total cost of ownership (depreciation, insurance, maintenance, fuel) for a VIN. Example: autodev_valuation({ vin: "1FTFW3LDXRFB40317", zip: "94103", _apiKey: "your-key" }) |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "auto-dev": {
      "url": "https://gateway.pipeworx.io/auto-dev/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/auto-dev/mcp` returns the tools in the table
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

Both URLs reach the same gateway and the same 1663+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

This pack takes your own API key (`_apiKey`) — we don't front one for it, so there's no curl here that would run without it. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/autodev_vin_decode`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "auto-dev": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-auto-dev"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-auto-dev
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Auto Dev data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT

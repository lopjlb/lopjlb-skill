# Enterprise HTTP MCP

Separate host protocol for Cursor / Claude Desktop / similar. **Not** the CLI product path.

| | |
|---|---|
| Endpoint | `https://www.lopjlb.com/bff/api/mcp` |
| Transport | Streamable HTTP (JSON-RPC) |
| Auth | `X-LOPJLB-API-Key` (live Enterprise entitlement) |
| Discovery | https://www.lopjlb.com/.well-known/mcp/server-card.json |

## Tools (typical)

- `get_market_regime`
- `search_tickers`
- `get_ticker_summary` (compact; uses signal-card internally)
- `get_ticker_detail`
- `get_screener_preview`
- `get_market_pulse`
- `get_earnings_intel`
- `get_intraday_snapshot`

## Cursor / Claude config sketch

```json
{
  "mcpServers": {
    "lopjlb": {
      "url": "https://www.lopjlb.com/bff/api/mcp",
      "headers": {
        "X-LOPJLB-API-Key": "lopjlb_live_…"
      }
    }
  }
}
```

Exact host config UI varies; always send the API key header and accept JSON.

## Smoke (curl)

```bash
curl -s https://www.lopjlb.com/bff/api/mcp \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -H "X-LOPJLB-API-Key: $LOPJLB_API_KEY" \
  -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2024-11-05","capabilities":{},"clientInfo":{"name":"curl","version":"0"}}}'
```

## Browser WebMCP (different)

Public in-page tools via `document.modelContext` (Chrome origin trial). Tools like `search_tickers`, `get_market_regime`, `get_market_pulse`, `open_ticker`. No Enterprise key. Distinct from this HTTP MCP server.

## Related

- https://www.lopjlb.com/auth.md
- https://www.lopjlb.com/developers
- Bulk export (REST, not MCP): `GET /bff/api/export/signals.csv`

# Enterprise HTTP MCP

Separate host protocol for Cursor / Claude Desktop / similar. **Not** the CLI product path.

| | |
|---|---|
| Endpoint | `https://www.lopjlb.com/bff/api/mcp` |
| Transport | Streamable HTTP (JSON-RPC) |
| Auth | `X-LOPJLB-API-Key` (live Enterprise entitlement) |
| Discovery | https://www.lopjlb.com/.well-known/mcp/server-card.json |
| Docs | https://www.lopjlb.com/developers#mcp |
| Version | 1.4.0 |

## Tools (typical)

- `get_metrics_schema` / `list_screener_presets` / `list_screener_scopes` (definitions — call before inventing columns)
- `get_market_regime` / `get_regime_history`
- `search_tickers`
- `get_ticker_summary` (compact; uses signal-card internally)
- `get_ticker_detail`
- `get_screener_preview` (anonymous preview slice)
- `screen_universe` — `asset` stocks|etf|crypto|index|commodity|all; `scope`; `preset`; `profile` compact|core|screener; limit ≤50
- `get_peers`
- `get_market_pulse`
- `get_congress_summary` / `get_congress_symbol`
- `get_prediction_markets_summary` / `get_prediction_markets_symbol`
- `get_earnings_calendar` / `get_economic_calendar`
- `get_treasury_rates`
- `get_exchange_hours` (optional `code`, `open_only`)
- `get_signal_ledger` / `get_signal_history`
- `get_sd50_feed` / `get_sd50_allocation` (allocation = Pro book via key)
- `get_universe_membership` / `get_universe_months` / `get_universe_maps`
- `get_earnings_intel`
- `get_intraday_snapshot` (per-symbol chart)
- `get_intraday_exhaustion` (I·EXH overlay — distinct from snapshot)

Prefer `get_metrics_schema` + `get_ticker_summary` + `screen_universe` (compact/core). Full screener book: CLI `lopjlb screen --profile screener`. Bulk CSV / archives stay REST/CLI (`lopjlb export`) — **export ≠ screener columns**.

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

- https://www.lopjlb.com/auth.md — Agent Registration / claim for **REST Bearer** (separate from MCP; key still required here)
- https://www.lopjlb.com/developers#mcp
- https://www.lopjlb.com/bff/api/schema/metrics
- Bulk export (REST, not MCP): `GET /bff/api/export/signals.csv` (thin)

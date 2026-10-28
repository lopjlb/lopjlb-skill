# Remote MCP (every plan)

Separate host protocol for Claude, ChatGPT, Cursor and similar. **Not** the CLI product path.

| | |
|---|---|
| Endpoint | `https://www.lopjlb.com/mcp` (legacy `https://www.lopjlb.com/bff/api/mcp` still works) |
| Transport | Streamable HTTP (JSON-RPC) |
| Plans | Every plan: Free, Essential, Research, Pro, Enterprise. Tools follow the signed-in plan |
| Auth | OAuth sign-in with a LOPJLB account (WorkOS AuthKit). Enterprise: `X-LOPJLB-API-Key` for headless use |
| OAuth metadata | https://www.lopjlb.com/.well-known/oauth-protected-resource/mcp |
| Server card | https://www.lopjlb.com/mcp/server-card |
| Docs | https://www.lopjlb.com/developers#mcp |

## What each plan gets

- **Free account:** regime, search, SPY25 summaries, calendars, option ledgers, congress.
- **Essential / Research:** the same tools at that desk's depth (S&P 500 book / full book).
- **Pro:** adds peers, I·EXH, SD50 allocation, and `ask_research_desk` at the Pro chat quota.
- **Enterprise:** adds API keys (headless MCP and REST), webhooks, bulk export, and unlimited research chat.

The tool list depends on the plan, so list it live with `tools/list`. The server card carries no tool list.

## Add it to a host

- **Claude:** Settings → Connectors → Add custom connector → paste `https://www.lopjlb.com/mcp`, then sign in.
- **Claude Code:** `claude mcp add --transport http lopjlb https://www.lopjlb.com/mcp`, then `/mcp` to sign in.
- **ChatGPT:** Settings → Apps & Connectors → developer mode (Advanced) → Create → paste the URL, choose OAuth, then sign in.
- **Cursor:** `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "lopjlb": { "url": "https://www.lopjlb.com/mcp" }
  }
}
```

Enterprise, headless (no browser sign-in):

```json
{
  "mcpServers": {
    "lopjlb": {
      "url": "https://www.lopjlb.com/mcp",
      "headers": { "X-LOPJLB-API-Key": "lopjlb_live_…" }
    }
  }
}
```

Exact host config UI varies. Never ask the user to paste an OAuth access token; the host runs the sign-in.

## How sign-in works

An unauthenticated call gets `401` with
`WWW-Authenticate: Bearer error="unauthorized", error_description="Authorization needed", resource_metadata="https://www.lopjlb.com/.well-known/oauth-protected-resource/mcp"`.
The metadata names AuthKit as the authorization server; the host runs OAuth and retries with
`Authorization: Bearer <token>`. The token only says who the user is; the plan comes from their subscription on every call,
never from token scopes. A claimed agent-registration token (https://www.lopjlb.com/auth.md) also works, at the claimant's plan up to Pro.

## Tools (typical; availability follows the plan)

- `get_metrics_schema` / `list_screener_presets` / `list_screener_scopes` (definitions — call before inventing columns)
- `get_market_regime` / `get_regime_history`
- `search_tickers`
- `get_ticker_summary` (compact; uses signal-card internally)
- `get_ticker_detail`
- `get_screener_preview` (SPY25 preview slice)
- `screen_universe` — `asset` stocks|etf|crypto|index|commodity|all; `scope`; `preset`; `profile` compact|core|screener; limit ≤50
- `get_peers` (Pro+)
- `get_market_pulse`
- `get_congress_summary` / `get_congress_symbol`
- `get_prediction_markets_summary` / `get_prediction_markets_symbol`
- `get_earnings_calendar` / `get_economic_calendar`
- `get_treasury_rates`
- `get_exchange_hours` (optional `code`, `open_only`)
- `get_signal_ledger` / `get_signal_history`
- `get_sd50_feed` / `get_sd50_allocation` (allocation: Pro+)
- `get_universe_membership` / `get_universe_months` / `get_universe_maps`
- `get_earnings_intel`
- `get_intraday_snapshot` / `get_intraday_exhaustion` (I·EXH; Pro+)
- `ask_research_desk` — NLP desk (same pipeline and daily quota as `/chat`); args `message`, optional `history` (≤2 Q&A), `session_id`; buffered markdown + route/symbol/followups. Prefer structured tools for screens.

Prefer `get_metrics_schema` + `get_ticker_summary` + `screen_universe` (compact/core). NLP desk: `ask_research_desk` or CLI `lopjlb ask`. Full screener book: CLI `lopjlb screen --profile screener`. Bulk CSV / archives stay REST/CLI (`lopjlb export`) — **export ≠ screener columns**.

## Smoke (curl, Enterprise key)

```bash
curl -s https://www.lopjlb.com/mcp \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -H "X-LOPJLB-API-Key: $LOPJLB_API_KEY" \
  -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2024-11-05","capabilities":{},"clientInfo":{"name":"curl","version":"0"}}}'
```

## Browser WebMCP (different)

Public in-page tools via `document.modelContext` (Chrome origin trial). Tools like `search_tickers`, `get_market_regime`, `get_market_pulse`, `open_ticker`. No sign-in. Distinct from this remote MCP server.

## Related

- https://www.lopjlb.com/auth.md — Agent Registration / claim for REST Bearer (a claimed token also reaches MCP)
- https://www.lopjlb.com/developers#mcp
- https://www.lopjlb.com/bff/api/schema/metrics
- Bulk export (REST, not MCP): `GET /bff/api/export/signals.csv` (thin)

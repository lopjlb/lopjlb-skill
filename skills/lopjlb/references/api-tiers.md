# LOPJLB API tiers

Base: `https://www.lopjlb.com/bff/api/`

## Free (no login)

| Capability | Notes |
|---|---|
| Regime, search, universe-preview, market pulse | Public |
| Thin `signal-card` | `depth=public` — direction, regime, price, sparkline, headline PF |
| Detail, earnings intel, fundamentals, 30d sparkline | Freemium; charts clamped |
| Browser screener | SPY25 sample only |

**Not Free:** full PF packs, composite/fund/best-combo on card, full `/universe`, peers, bulk export, MCP, webhooks, intraday suite.

## Pro (WorkOS session + pro entitlement)

| Capability | Notes |
|---|---|
| Full universe screener UI | Filters, saved views |
| Full `signal-card` | `depth=full` via session cookie on BFF |
| Screener UI CSV | Filtered view only — not Enterprise bulk snapshot |
| Long charts, email alerts, peers, chat, plays | Browser workflow |

Pro is **browser-first**. Headless `/universe` without an Enterprise key is not the product path (Enterprise key maps to Pro REST).

## Enterprise (live entitlement + API key)

| Capability | Notes |
|---|---|
| `X-LOPJLB-API-Key` | Mint in Settings after entitlement is active |
| Full signal-card + Pro REST | `/bigfive/universe`, peers, `lopjlb screen` |
| HTTP MCP | `POST /bff/api/mcp` |
| Bulk latest-signal export | CSV / Parquet — `lopjlb export signals --out …` |
| Webhooks, intraday | Slack/Discord; live scanner / 1D charts |

Lapsed entitlement → HTTP `402 enterprise_required`.

## signal-card `depth`

```bash
# Anon
curl -s https://www.lopjlb.com/bff/api/bigfive/signal-card/AAPL
# → depth=public

# Enterprise key
curl -s -H "X-LOPJLB-API-Key: $LOPJLB_API_KEY" \
  https://www.lopjlb.com/bff/api/bigfive/signal-card/AAPL
# → depth=full
```

**Public fields (teaser):** symbol, display_symbol, name, sector, asset_class, direction, score, regime, hmm_days_in_regime, current_price, daily_change, sparkline, pf, win_rate, market_cap, depth.

**Full pack adds:** PF packs (base / HMM / composite), best-combo, composite_score, fund scores / archetype, ATR / exhaustion / coil, opt metrics, and related research fields.

## Rate limits

| Caller | Per minute | Per day |
|---|---|---|
| Anonymous / session (IP) | 120 | 10,000 |
| Anon freemium ticker (signal-card / detail / intel) | 30 | 500 |
| Enterprise API key | 600 | 50,000 |

## Errors

- `401` — invalid / revoked key
- `402` — `pro_required` or `enterprise_required` (body may include `upgrade_url`)
- `429` — rate limited

## Docs

- https://www.lopjlb.com/pricing.md
- https://www.lopjlb.com/developers
- https://www.lopjlb.com/api/llms.txt

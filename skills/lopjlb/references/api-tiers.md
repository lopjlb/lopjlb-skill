# Free vs Pro vs Enterprise (exact behavior)

Base: `https://www.lopjlb.com/bff/api/`

LOPJLB is a **research layer**. Tiers gate **depth of opinion + cross-section access**, not whether prices exist.

## Identity (how agents authenticate)

| Path | When | How |
|---|---|---|
| **Free** | Default | No login — CLI/curl/BFF public + freemium |
| **Agent claim** | User asks to auth / bind account | Follow https://www.lopjlb.com/auth.md (or `lopjlb auth claim`). Show AuthKit `verification_uri`, ask for page code, complete claim, Bearer on BFF. Claim binds `act.sub`; **Pro = Stripe/IAP on that user**, not a static agent scope. Do not invent lopjlb.com `/claim`. |
| **Pro session** | Browser desk | WorkOS cookie + entitlement |
| **Enterprise key** | Headless Pro REST / MCP / export | `LOPJLB_API_KEY` / `X-LOPJLB-API-Key`. Agent Bearer does **not** unlock MCP. |

## One-page matrix

| Capability | Free (no login) | Pro (WorkOS session) | Enterprise (live key/session) |
|---|---|---|---|
| Positioning | Thin research teaser + freemium ticker stack | Full browser research desk | Headless agents + ops |
| `GET …/signal-card/{SYM}` | `depth=public` teaser (direction, regime, price, sparkline, headline PF) | `depth=full` (PF packs, composite, fund scores, best-combo, overlays) | Same full pack via `X-LOPJLB-API-Key` |
| `GET …/detail/{SYM}` · intel · fundamentals · info | Freemium OK (ticker budget) | Same + higher quote batch caps | Same |
| Sparkline / charts | ~30d clamp | Long history | Long + **intraday** charts |
| Screener | SPY25 sample / `universe-preview` | Full 12k+ UI, filters, saved views, **UI CSV of filtered view** | UI + headless 5× `/universe*` (`lopjlb screen --asset`) |
| Metric / preset / scope schema | `GET …/schema/metrics\|presets\|scopes` | Same | Same |
| Peers / chat / SD50 allocation CSV·JSON | — / public SD50 **feed** only | Yes (browser) | Yes (+ key); CLI `sd50 allocation` |
| `GET …/hmm/latest` · pulse · calendars · congress · predict · treasuries · ledger | Yes | Yes | Yes |
| `GET …/hmm/history` | Last ~1y (`truncated=true`) | Full with `?full=1` | Full with key |
| `GET …/universes/spy25\|spy500` · maps | Yes (PIT membership; ≠ live SP500 screen) | Yes | Yes |
| Bulk latest signals | — | — | Thin `export/signals.csv` (≠ screener book) |
| Historical SD50 / backtest packs | — | — | `GET …/export/archives/…` |
| HTTP MCP `POST …/mcp` | — (browser WebMCP only) | — | Yes (multi-asset screen + schema tools) |
| Webhooks / I·EXH live scanner | — | — | Yes (`lopjlb iexh` / MCP `get_intraday_exhaustion`) |
| Rate limits | 120/min · 10k/day IP; ticker 30/min · 500/day | Session (same IP budget) | Key 600/min · 50k/day |

Pro is **browser-first**. Headless `/universe` without an Enterprise key is not the product path (keys map to Pro REST).

## Endpoint behavior cheat sheet

| Endpoint / CLI | Free | Pro session | Enterprise key |
|---|---|---|---|
| `lopjlb ticker SYM` → signal-card | thin | full | full |
| `lopjlb ticker SYM intel` | yes (budget) | yes | yes |
| `lopjlb preview` | yes | yes | yes |
| `lopjlb schema metrics` | yes | yes | yes |
| `lopjlb screen --asset …` | 401/402 | browser cookie path | **yes** (product headless path) |
| `lopjlb peers SYM` | 402 | yes | yes |
| `lopjlb sd50 allocation` | 402 | yes | yes |
| `lopjlb regime-history` | ~1y | `--full` | `--full` |
| `lopjlb export signals --out …` | 402 | 402 | yes (thin) |
| `lopjlb export archive …` | 402 | 402 | yes |
| `lopjlb iexh` | 402 | 402 | yes |
| `lopjlb intraday SYM` | 402 | 402 | yes |
| `POST /mcp` tools | — | — | yes |

## signal-card `depth` (exact)

```bash
# Anon → depth=public
curl -s https://www.lopjlb.com/bff/api/bigfive/signal-card/AAPL | jq .depth

# Enterprise key → depth=full
curl -s -H "X-LOPJLB-API-Key: $LOPJLB_API_KEY" \
  https://www.lopjlb.com/bff/api/bigfive/signal-card/AAPL | jq .depth
```

**Public teaser fields:** symbol, name, sector, direction, score, regime, hmm_days_in_regime, price, daily_change, sparkline, headline `pf` / `win_rate`, market_cap, `depth`.

**Full pack adds:** PF packs (base / HMM / composite), best-combo, composite_score, fund scores / archetype, ATR / exhaustion / coil, opt metrics, related research fields.

Field meanings: [glossary.md](glossary.md).

## Errors

| HTTP | Meaning |
|---|---|
| 401 | Auth / invalid key |
| 402 | `pro_required` or `enterprise_required` |
| 429 | Rate limited |

## Docs

- https://www.lopjlb.com/pricing.md · https://www.lopjlb.com/developers · https://www.lopjlb.com/api/llms.txt

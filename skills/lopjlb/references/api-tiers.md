# Free vs Essential vs Research vs Pro vs Enterprise

Base: `https://www.lopjlb.com/bff/api/`

LOPJLB is a **research layer**. Tiers gate **depth of opinion + cross-section access**, not whether prices exist.

## Identity (how agents authenticate)

| Path | When | How |
|---|---|---|
| **Free** | Default | No login — CLI/curl/BFF public + freemium |
| **Agent claim** | User asks to auth / bind account | Follow https://www.lopjlb.com/auth.md (or `lopjlb auth claim`). Show AuthKit `verification_uri`, ask for page code, complete claim, Bearer on BFF. Claim binds `act.sub`; **Essential / Research / Pro follow Stripe/IAP on that user**, not a static agent scope. Do not invent lopjlb.com `/claim`. |
| **Paid desk session** | Browser desk | WorkOS cookie + `essential`, `research`, or `pro` entitlement |
| **MCP sign-in** | Host app (Claude, ChatGPT, Cursor) | Add `https://www.lopjlb.com/mcp`; the host runs OAuth and the user signs in with their LOPJLB account. Every plan; tools follow that plan. See [mcp.md](mcp.md). |
| **Enterprise key** | Headless Pro REST / export / MCP without a browser | `LOPJLB_API_KEY` / `X-LOPJLB-API-Key`. |

## One-page matrix

| Capability | Free | Essential ($79) | Research ($129) | Pro ($249) | Enterprise ($1,599) |
|---|---|---|---|---|---|
| Positioning | SPY25 teaser + freemium ticker stack | S&P 500 daily desk | Full 12k+ daily book | Complete live browser desk | Headless agents + ops |
| `GET …/signal-card/{SYM}` | `depth=public` teaser | Fund + exhaustion on S&P 500 | Fund + exhaustion on full book | Full PF/composite/best-combo pack | Same full pack via key |
| `GET …/detail/{SYM}` · intel · fundamentals · info | Yes (ticker budget) | Yes | Yes | Yes + higher quote batch caps | Same |
| Charts | ~30d clamp | Through 1Y | Through 5Y / MAX | Long history + ATM IV + live 1D | Same + CLI via key |
| Screener | SPY25 preview | S&P 500 | Full 12k+ universe + UI CSV | Full book + I·EXH + options | Pro REST + headless `/universe*` |
| Research chat | Guest 1/day · signed-in 3/day | 3/day | 3/day | 10/day | Unlimited |
| Peers / SD50 allocation | — / public feed only | — | — | Yes | Yes |
| Live I·EXH / intraday | — | — | — | Browser | Browser + CLI/key |
| Remote MCP (`/mcp`, sign in) | Free tools: regime, search, SPY25 summaries, calendars, ledgers, congress | S&P 500 desk | Full book | Complete desk (peers, I·EXH, SD50 allocation) | Same + headless via key |
| Webhooks / bulk export | — | — | — | — | Yes |
| Rate limits | 120/min · 10k/day IP; ticker 30/min · 500/day | Session/IP budget | Session/IP budget | Session/IP budget | Key 600/min · 50k/day |

Paid desk tiers are **browser-first**. MCP is on every plan after sign-in. Headless screening and export use an Enterprise key (which also unlocks Pro REST and MCP without a browser).

## Endpoint behavior cheat sheet

| Endpoint / CLI | Free | Paid browser session | Enterprise key |
|---|---|---|---|
| `lopjlb ticker SYM` → signal-card | thin | Depth follows paid tier | full |
| `lopjlb ticker SYM intel` | yes (budget) | yes | yes |
| `lopjlb ask "…"` → `/research/chat` | yes (anon/free caps) | yes (tier cap) | yes (unlimited) |
| MCP `ask_research_desk` | signed-in free quota | yes (tier cap) | yes (unlimited) |
| `lopjlb preview` | yes | yes | yes |
| `lopjlb schema metrics` | yes | yes | yes |
| `lopjlb screen --asset …` | 401/402 | Browser desk is the supported session path | **yes** (product headless path) |
| `lopjlb peers SYM` | 402 | Pro only | yes |
| `lopjlb sd50 allocation` | 402 | Pro only | yes |
| `lopjlb regime-history` | ~1y | Long-history access follows tier | `--full` |
| `lopjlb export signals --out …` | 402 | 402 | yes (thin) |
| `lopjlb export archive …` | 402 | 402 | yes |
| `lopjlb iexh` | 402 | 402 (CLI needs key) | yes |
| `lopjlb intraday SYM` | 402 | 402 (CLI needs key) | yes |
| `POST https://www.lopjlb.com/mcp` tools | after sign-in, free tools | after sign-in, tier tools | yes (key or sign-in) |

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
| 402 | `essential_required`, `research_required`, `pro_required`, or `enterprise_required` |
| 429 | Rate limited |

## Docs

- https://www.lopjlb.com/pricing.md · https://www.lopjlb.com/developers · https://www.lopjlb.com/api/llms.txt

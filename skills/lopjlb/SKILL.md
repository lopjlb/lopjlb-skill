---
name: lopjlb
description: >
  Use LOPJLB quantitative U.S. equity/ETF research via BFF REST, lopjlb CLI/PyPI,
  or Enterprise HTTP MCP. Daily signals, HMM regime, thin free signal-card teasers,
  Pro screener, Enterprise keys/export/intraday. Use when the user asks about stock
  signals, regime, screener shortlists, lopjlb CLI, signal-card, or LOPJLB API/MCP.
  Do NOT use for options flow, on-chain DeFi, futures, non-U.S. markets, personalized
  investment advice, or trade execution.
metadata:
  author: LOPJLB Research
  version: 1.0.0
  category: finance-research
  tags: [equities, etf, signals, regime, hmm, screener, cli, mcp, bff]
  documentation: https://www.lopjlb.com/developers
  llms: https://www.lopjlb.com/llms.txt
  pricing: https://www.lopjlb.com/pricing.md
  auth: https://www.lopjlb.com/auth.md
---

# LOPJLB research API / CLI / MCP

Independent quantitative research desk: 12,000+ U.S.-listed symbols ranked daily with
backtested signals, profit factors, Sharpe, and HMM market regime context.

## Prefer live docs

If unsure about endpoints or tiers, fetch:

- https://www.lopjlb.com/llms.txt
- https://www.lopjlb.com/api/llms.txt
- https://www.lopjlb.com/pricing.md
- https://www.lopjlb.com/developers

## Hard rules

1. **Base URL only:** `https://www.lopjlb.com/bff/api/` — never call `api.lopjlb.com`.
2. **Free `signal-card` is thin** (`depth=public`): direction, regime, price, sparkline, headline PF. Full PF packs / composite / best-combo need Pro session or Enterprise key (`depth=full`).
3. **Do not rebuild the universe** by looping free ticker calls. Cross-section = Pro UI or Enterprise `/universe` / `lopjlb screen` / export.
4. **CLI product path is REST.** HTTP MCP is a separate Enterprise host protocol at `POST /bff/api/mcp`. Do not treat CLI as an MCP client.
5. **Escalate credentials only when the user has them.** Default to Free CLI/curl.

## Decision tree

| User need | Path |
|---|---|
| Single ticker research | `lopjlb ticker SYM` or `GET .../signal-card/{SYM}` (+ detail / intel) |
| Market regime / pulse | `lopjlb regime` / `GET .../hmm/latest` / `GET .../market-pulse` |
| Cross-section screen | Pro browser screener, or Enterprise `lopjlb screen` / `/bigfive/universe` |
| Bulk latest signals | Enterprise `lopjlb export signals --out file.csv` |
| Cursor/Claude host tools | Enterprise MCP → see [references/mcp.md](references/mcp.md) |

## Free quickstart (no login)

```bash
uv tool install lopjlb
# or: curl -fsSL https://www.lopjlb.com/install.sh | sh
# or: brew install lopjlb/tap/lopjlb

lopjlb ticker AAPL
lopjlb ticker AAPL intel
lopjlb regime
lopjlb schema
```

```bash
curl -s https://www.lopjlb.com/bff/api/hmm/latest
curl -s https://www.lopjlb.com/bff/api/bigfive/signal-card/AAPL   # depth=public
curl -s https://www.lopjlb.com/bff/api/bigfive/detail/AAPL
curl -s https://www.lopjlb.com/bff/api/bigfive/earnings-intel/AAPL
```

Anon freemium ticker routes (`signal-card` / `detail` / `earnings-intel`): **30/min · 500/day** per IP (plus global 120/min · 10k/day).

## Enterprise (when user has a key)

Mint in Settings after Enterprise entitlement is active. Header: `X-LOPJLB-API-Key`.

```bash
export LOPJLB_API_KEY=lopjlb_live_…
lopjlb screen
lopjlb export signals --out signals.csv
```

Keys unlock full `signal-card`, Pro REST (`/universe`, peers), MCP, export, intraday.

## Errors

| HTTP | Meaning |
|---|---|
| 401 | Invalid / revoked key |
| 402 | `pro_required` or `enterprise_required` |
| 429 | Rate limited |

## References

- [api-tiers.md](references/api-tiers.md) — Free / Pro / Enterprise + `depth` + rate limits
- [cli.md](references/cli.md) — install, commands, exit codes
- [mcp.md](references/mcp.md) — Enterprise HTTP MCP for hosts

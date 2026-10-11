# lopjlb-skill

Agent Skill for [LOPJLB](https://www.lopjlb.com) — a **quant research layer**, not a market-data API.

If you want raw OHLCV, free tools (yfinance, broker APIs, Finviz, TradingView) are enough.
If you want **daily, backtested, regime-aware research opinions** with narrative synthesis across 12,000+ U.S. names, LOPJLB is the value add.

This skill teaches hosts (Cursor / Claude Code / Codex) how to call the **public surface** (BFF REST, CLI, Enterprise MCP). It does **not** ship proprietary signal engines or the private monorepo.

## Verdict (read this first)

LOPJLB sits on top of market data and turns it into a **nightly opinionated workflow**:

| You get | Meaning |
|---|---|
| A **signal**, not just prices | Direction, score, regime, profit factor, Sharpe, win rate, expectancy, best-combo |
| **Context by default** | Market regime + breadth; whether the tape is helping or hurting the name |
| **Synthesis**, not just facts | Earnings CallCards → multi-quarter narrative (themes, guidance changes) |
| A **full research stack** per symbol | Profile, short interest, chart + overlays, backtests, fundamentals, scores |
| A **universe-ranking system** | Nightly re-score of 12k+ U.S. names — the product is the cross-section, not one-off lookups |

**Compare:** [yfinance / Schwab / Finviz / TradingView](skills/lopjlb/references/positioning.md) · [Tier access matrix](skills/lopjlb/references/api-tiers.md) · [Morning workflows](skills/lopjlb/references/workflows.md)

## Install

```bash
npx skills add lopjlb/lopjlb-skill
# or
npx skills add https://github.com/lopjlb/lopjlb-skill
```

Then ask your agent about U.S. equity signals, HMM regime, `lopjlb ticker AAPL`, or the NLP desk (`lopjlb ask` / MCP `ask_research_desk`).

## What you get (shapes)

```bash
lopjlb regime
# → { "success": true, "regime": { "regime": "BULL", "days_in_regime": 10, "date": "…" } }

lopjlb ticker AAPL
# → { "symbol": "AAPL", "direction": "BUY", "regime": "…", "depth": "public", … }

lopjlb ask "vidia …" --payload
# → { "route": "ticker_context", "symbol": "NVDA", "response": "### Snapshot…", "embeds": [] }

lopjlb ask "upcomin economic event" --payload
# → { "route": "desk", "symbol": null, "response": "### Economic Calendar…" }
```

Full recipes + Enterprise `screen` / `schema metrics` samples: [skills/lopjlb/references/workflows.md](skills/lopjlb/references/workflows.md).

## Layout

```
skills/lopjlb/SKILL.md                 # agent entrypoint
skills/lopjlb/references/
  positioning.md                       # why not yfinance / Finviz / TV
  api-tiers.md                         # Free / Essential / Research / Pro / Enterprise
  glossary.md                          # scores, regime, depth, flags
  workflows.md                         # morning research recipes + sample shapes
  cadence.md                           # nightly vs intraday vs delayed
  cli.md · mcp.md                      # surface detail
```

## Maintainers

Source of truth: private monorepo `packaging/lopjlb-skill/`.

```bash
rsync -a --delete packaging/lopjlb-skill/ /path/to/lopjlb-skill/ --exclude .git
cd /path/to/lopjlb-skill && git add -A && git commit -m "sync skill" && git push
```

## Links

- https://www.lopjlb.com/how-it-works — nightly desk loop
- https://www.lopjlb.com/methodology — signal specs
- https://www.lopjlb.com/compare — platform compare
- https://www.lopjlb.com/developers — API / agents
- https://www.lopjlb.com/llms.txt · https://www.lopjlb.com/pricing.md · https://www.lopjlb.com/auth.md

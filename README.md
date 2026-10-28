# lopjlb-skill

Everything an agent needs for [LOPJLB](https://www.lopjlb.com): the Agent Skill, the Claude Code plugin, the remote MCP config and the MCP Registry entry. LOPJLB is a **quant research layer**, not a market-data API.

If you want raw OHLCV, free tools (yfinance, broker APIs, Finviz, TradingView) are enough.
If you want **daily, backtested, regime-aware research opinions** with narrative synthesis across 12,000+ U.S. names, LOPJLB is the value add.

This skill teaches hosts (Cursor / Claude Code / Codex) how to call the **public surface** (BFF REST, CLI, remote MCP on every plan). It does **not** ship proprietary signal engines or the private monorepo.

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

### Claude Code plugin (skill + remote MCP)

```text
/plugin marketplace add lopjlb/lopjlb-skill
/plugin install lopjlb@lopjlb
```

The plugin bundles this skill and the remote MCP server at
`https://www.lopjlb.com/mcp`. On first use Claude asks you to sign in with your
LOPJLB account (OAuth); the tools you get follow your plan, and the free plan
works.

### Remote MCP only

Add `https://www.lopjlb.com/mcp` as a custom connector (Claude, ChatGPT
developer mode) or in `mcp.json` (Cursor, VS Code). Sign in when asked.

### CLI and Python (`lopjlb`)

The skill's examples call the `lopjlb` CLI. Free research needs no key and no
browser.

```bash
brew install lopjlb/tap/lopjlb        # Homebrew (github.com/lopjlb/homebrew-tap)
uv tool install lopjlb                # or: pipx install lopjlb
curl -fsSL https://www.lopjlb.com/install.sh | sh
```

`import lopjlb` exposes the same REST client from Python
([PyPI](https://pypi.org/project/lopjlb/)).

Then ask your agent about U.S. equity signals, HMM regime, `lopjlb ticker AAPL`, or the NLP desk (`lopjlb ask` / MCP `ask_research_desk`).

## What you get (shapes)

```bash
lopjlb regime
# → { "success": true, "regime": { "regime": "BULL", "days_in_regime": 10, "date": "…" } }

lopjlb ticker AAPL
# → { "symbol": "AAPL", "direction": 1, "regime": "BULL", "vintage": "2026-10-27", … }   (1 = BUY, -1 = SELL)

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
  robinhood.md                         # read-only recipe beside Robinhood's own tools
.claude-plugin/plugin.json             # Claude Code plugin manifest
.claude-plugin/marketplace.json        # lets `/plugin marketplace add lopjlb/lopjlb-skill` work
.mcp.json                              # remote MCP (https://www.lopjlb.com/mcp, OAuth)
server.json                            # MCP Registry entry (com.lopjlb/research)
```

## Maintainers

Source of truth: `packaging/lopjlb-skill/` in the private monorepo. Every merge
to its `main` that touches that folder is mirrored here by the
`sync-skill-repo` workflow, and a new `.claude-plugin/plugin.json` version is
tagged `v<version>`. Don't edit this repo directly: the next sync overwrites
it. Bump `version` in `plugin.json` and `metadata.version` in `SKILL.md`
together; the workflow refuses a mismatch.

`server.json` is the [MCP Registry](https://registry.modelcontextprotocol.io)
entry (`com.lopjlb/research`, DNS-verified for lopjlb.com). Publish a new
server version with `mcp-publisher publish server.json` after
`mcp-publisher login dns --domain lopjlb.com`.

## Links

- https://www.lopjlb.com/how-it-works — nightly desk loop
- https://www.lopjlb.com/methodology — signal specs
- https://www.lopjlb.com/compare — platform compare
- https://www.lopjlb.com/developers — API / agents
- https://www.lopjlb.com/llms.txt · https://www.lopjlb.com/pricing.md · https://www.lopjlb.com/auth.md

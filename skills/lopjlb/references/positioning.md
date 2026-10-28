# Why LOPJLB (not yfinance / Schwab / Finviz / TradingView / GuruFocus)

LOPJLB is **not primarily a data API**. It is a quant research layer on top of market data: a nightly desk that re-scores thousands of U.S. tickers and ships **evidence with the call**.

## Side-by-side (AAPL mental model)

| Need | yfinance / broker API | Finviz / TradingView | GuruFocus | LOPJLB |
|---|---|---|---|---|
| Last price / OHLCV | Excellent | Excellent | Support | Available as support, not the product |
| DIY indicators / charts | You build | You build / chart | Limited | Nightly published signals + overlays |
| DCF / owner-earnings models | DIY | Rare | Excellent | Not the product (FUND scores absorb inputs) |
| Guru / 13F portfolio tracking | DIY | Rare | Excellent | No — not a guru-tracker |
| Per-ticker backtested call | No | Rare / opaque | No | Direction + PF / Sharpe / win rate / expectancy |
| Market regime gating | DIY | Limited | Limited | HMM regime on every name + history |
| Universe rank (12k+) | DIY | Screener filters | Screener filters | Nightly scored cross-section (Pro / Enterprise) |
| Earnings narrative | Transcript dump | Headlines | Calendar + estimates | Multi-quarter CallCard synthesis (themes, guidance) |
| PIT mega-cap / SPX panels | DIY | Partial | Partial | `/universes/spy25` · `/universes/spy500` |
| Agent / CLI / MCP | DIY glue | Weak | Weak | First-class BFF + `lopjlb` CLI + remote MCP (every plan) |

## What free market tools already do well

- Pull prices, fundamentals dumps, option chains, news headlines
- Charting and drawing tools
- Broker order entry

Use those when the job is **data acquisition** or **execution**.

## What GuruFocus already does well

- DCF and owner-earnings valuation models
- Ratio calculators and deep fundamental history
- Guru / 13F portfolio databases

Use GuruFocus when the job is **modeling intrinsic value**. Use LOPJLB when the job is a **nightly, regime-aware directional opinion with evidence**.

## What LOPJLB adds

1. **Opinionated daily signals** — BUY/SELL (and flat) with attached out-of-sample stats, not a blank indicator canvas.
2. **Regime-aware context** — bull / recovery / ranging / bear / crisis labels that condition interpretation.
3. **Synthesis** — earnings intel turns calls into a multi-quarter research memo.
4. **Ranked universe** — the workflow assumes you screen the cross-section, then drill one name.
5. **Evidence packaging** — score breakdowns, best-combo setups, track-record ledger, SD50 allocation history.

## Explicit non-goals

- Not a replacement for broker market data feeds
- Not options flow, on-chain DeFi, futures, or non-U.S. primary coverage
- Not personalized investment advice or trade execution
- Not an open-source dump of the signal engines (this skill wraps the public API only)
- Not IBKR portfolio / option-chain MCP (use trading_skills if you need a broker)
- Not Plaid / personal bank sync (BankSync)
- Not DCF Excel generation (Anthropic financial-modeling skill) — we supply research inputs
- Not a GuruFocus clone (calculators, owner-earnings encyclopedias, 13F guru trackers)
- Not a local ARIMA/GARCH package dump (K-Dense Scientific Skills for methodology)

## Complementary stack

| Job | Tool |
|---|---|
| Desk opinion + universe + CallCards | **LOPJLB** (this skill / CLI / MCP) |
| DCF / intrinsic-value modeling | GuruFocus or Anthropic financial-modeling skill |
| Local econometrics / Monte Carlo | K-Dense Scientific Skills |
| Broker options / rolls / account | trading_skills (IBKR) |
| Personal bank balances | BankSync MCP |

## One-line verdict

**Raw market data → free tools. Valuation encyclopedias → GuruFocus. Daily backtested, regime-aware research opinions with narrative synthesis → LOPJLB.**

Live product pages: [how it works](https://www.lopjlb.com/how-it-works) · [compare](https://www.lopjlb.com/compare) · [vs GuruFocus](https://www.lopjlb.com/compare/gurufocus) · [methodology](https://www.lopjlb.com/methodology) · [schema](https://www.lopjlb.com/bff/api/schema/metrics)

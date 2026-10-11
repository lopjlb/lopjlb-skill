# Refresh cadence & staleness

Agents should say what is **nightly**, **intraday**, or **delayed** — not pretend everything is live.

## Nightly (core research desk)

Updates after the U.S. cash close; morning-ready:

- Directional signals, PERF / fund scores, best-combo / composite ranks
- Per-ticker backtest stats attached to the call (PF, Sharpe, win rate, …)
- HMM regime classification + `regime_history` append
- Universe rankings used by screener / `lopjlb screen` / export snapshots
- SD50 month-end style allocation artifacts (when the monthly job runs)

Staleness rule of thumb: treat signal fields as **prior session / overnight** unless the payload itself says otherwise.

## Intraday (Enterprise / selected public)

| Surface | Cadence |
|---|---|
| Live I·EXH scanner / intraday charts | Pro (browser); bars/polling (seconds–minutes). CLI/MCP via Enterprise key |
| SD50 MTD tracker | Public; short CDN / poller cache |
| Live quotes batch | Freemium; provider-delayed quotes, batch-capped |
| Prediction markets / some news | Poller (~tens of minutes), not tick |

## Delayed / cached public context

| Surface | Notes |
|---|---|
| Congress STOCK Act | Filing lag; aggregates refresh from sync jobs |
| Calendars (earnings / econ) | Vendor calendar lag |
| Treasuries | Daily curve poll |
| Market pulse breadth | Cached aggregate (minutes), not a tick feed |
| PIT `/universes/*` | Monthly seed files; `asof` is the membership month, not “live holdings” |

## What users should not assume

- Free `signal-card` price/sparkline ≠ real-time tape
- Nightly BUY does not update mid-session on Free/Pro signal packs
- Pro/Enterprise intraday does **not** rewrite the nightly research opinion unless the product surface says so

Product walkthrough: https://www.lopjlb.com/how-it-works

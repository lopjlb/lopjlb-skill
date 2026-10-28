# lopjlb CLI / PyPI

Thin REST client for the **quant research layer** (signals, regime, intel) — not a
yfinance clone. Talks only to `https://www.lopjlb.com/bff/api/`.

Workflows + sample shapes: [workflows.md](workflows.md). Field meanings: [glossary.md](glossary.md).

## Install

```bash
uv tool install lopjlb
# or
curl -fsSL https://www.lopjlb.com/install.sh | sh
# or
brew install lopjlb/tap/lopjlb
# or
pip install lopjlb
```

## Agent claim (optional)

```bash
lopjlb auth claim --email you@example.com   # opens AuthKit verification URI; paste page code
lopjlb auth token --print                   # short-lived Bearer (advanced)
```

Or follow https://www.lopjlb.com/auth.md in a host agent (show link → ask for code). Free CLI needs no claim.

## Free (no key)

```bash
lopjlb ticker AAPL              # thin signal-card + summary
lopjlb ticker AAPL intel        # earnings CallCard / rollup
lopjlb ticker AAPL scores
lopjlb ticker AAPL info
lopjlb ticker AAPL chart
lopjlb regime
lopjlb regime-history           # public ~1y HMM history
lopjlb ask "What is the current market regime?"
lopjlb ask "AAPL signal brief" --payload   # done JSON: route/symbol/response/followups/embeds
# Sample done-frames (ticker_context / desk / chart embeds): see workflows.md § NLP desk
lopjlb pulse
lopjlb hours                    # world exchange open/closed (/market-hours)
lopjlb hours --code NYSE
lopjlb hours --open-only
lopjlb search NVDA
lopjlb preview                  # universe preview (not full 12k)
lopjlb universes spy25          # PIT mega-cap membership (optional --asof YYYY-MM)
lopjlb universes spy500 --asof 2024-12
lopjlb universes months --for spy25
lopjlb universes maps           # renames + delists
lopjlb congress                 # STOCK Act summary (or: congress AAPL)
lopjlb predict                  # prediction markets summary (or: predict AAPL)
lopjlb calendar earnings        # 7 days back → 14 ahead (--days-back / --days-ahead, max 120)
lopjlb calendar econ
lopjlb treasuries               # US yield curve (/treasury-rates)
lopjlb ledger
lopjlb signal-history
lopjlb sd50
lopjlb schema                   # CLI → REST map + schema URL pointers
lopjlb schema metrics           # 180 screener columns (ui_id ↔ api_key)
lopjlb schema presets
lopjlb schema scopes
lopjlb ticker AAPL income       # BDC/REIT/CEF/ETF/pref income.metrics
lopjlb doctor
```

Stdout is JSON (pretty on TTY). Errors → stderr JSON with `retryable`.

### What you actually get (illustrative shapes)

Exact keys drift; always read live JSON. These show **intent**:

`lopjlb regime` → `{ "success": true, "regime": { "regime": "BULL", "days_in_regime": 10, "date": "…" }, … }`

`lopjlb ticker AAPL` → thin card: `direction`, `regime`, headline `pf`, `depth: "public"`, `stock_url`, `next_actions`

`lopjlb ticker AAPL intel` → CallCard / multi-quarter rollup (themes, guidance) — LLM-assisted on sourced transcripts

`lopjlb preview` → ranked **sample** rows (SPY25-style), not the full universe

`lopjlb universes spy25 --asof 2025-01` → `{ "asof": "2025-01", "count": 25, "members": [ { "symbol", "name", "weight" }, … ] }`

## Enterprise key

```bash
export LOPJLB_API_KEY=lopjlb_live_…
# or: lopjlb --key lopjlb_live_… …

lopjlb screen --asset all --profile screener   # merge 5 partitions (Pro REST via key)
lopjlb screen --asset etf --scope WEEKLY --limit 50 --profile core
lopjlb screen --preset momentum-pullback --limit 25
lopjlb peers AAPL
lopjlb sd50 allocation          # Pro allocation JSON (not public L1 feed)
lopjlb iexh --limit 40          # I·EXH overlay (API key)
lopjlb regime-history --full    # full HMM history since ~2010
lopjlb export signals --out signals.csv   # thin pkl snapshot — not screener book
lopjlb export archive --source sd50                 # list months
lopjlb export archive --source sd50 --month 2024_12 # list files
lopjlb export archive --source sd50 --month 2024_12 \
  --file monthly_sd50_allocation_2024_12.csv --out sd50.csv
lopjlb intraday AAPL
```

Mint keys in https://www.lopjlb.com/settings after Enterprise entitlement is active.

**Note:** `export/signals.csv` ≠ full screener columns. For screener-shaped rows use `lopjlb screen --profile screener`. PIT `universes spy500` ≠ live SP500 screen `--scope SP500`.

## Exit codes

| Code | Meaning |
|---|---|
| 0 | ok |
| 1 | error |
| 2 | usage / missing dependency |
| 3 | auth (HTTP 401) |
| 4 | entitlement (HTTP 402) |
| 5 | rate_limited (HTTP 429) |
| 6 | network (no HTTP response: DNS, connect, TLS, timeout) |

Errors are one JSON line on stderr. `retryable` is true for 429, 5xx and
`{"error": "network"}`; GETs already retried twice with backoff before that.

## Python

```python
import lopjlb

print(lopjlb.regime())
t = lopjlb.Ticker("AAPL")
print(t.summary())   # thin card without key
print(t.intel())

client = lopjlb.Client(api_key="lopjlb_live_…")  # Enterprise
```

## MCP note

CLI does **not** call MCP for product commands. Optional smoke only:

```bash
lopjlb debug mcp tools
```

Hosts (Claude, ChatGPT, Cursor) connect to `https://www.lopjlb.com/mcp` directly and sign in — see [mcp.md](mcp.md).

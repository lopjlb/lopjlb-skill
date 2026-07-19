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

## Free (no key)

```bash
lopjlb ticker AAPL              # thin signal-card + summary
lopjlb ticker AAPL intel        # earnings CallCard / rollup
lopjlb ticker AAPL scores
lopjlb ticker AAPL info
lopjlb ticker AAPL chart
lopjlb regime
lopjlb regime-history           # public ~1y HMM history
lopjlb pulse
lopjlb search NVDA
lopjlb preview                  # universe preview (not full 12k)
lopjlb universes spy25          # PIT mega-cap membership (optional --asof YYYY-MM)
lopjlb universes spy500 --asof 2024-12
lopjlb universes months --for spy25
lopjlb universes maps           # renames + delists
lopjlb congress                 # STOCK Act summary (or: congress AAPL)
lopjlb predict                  # prediction markets summary (or: predict AAPL)
lopjlb calendar earnings
lopjlb calendar econ
lopjlb treasuries
lopjlb ledger
lopjlb signal-history
lopjlb sd50
lopjlb schema                   # CLI → REST map (prefer over guessing MCP)
lopjlb doctor
```

Stdout is JSON (pretty on TTY). Errors → stderr JSON with `retryable`.

### What you actually get (illustrative shapes)

Exact keys drift; always read live JSON. These show **intent**:

`lopjlb regime` → `{ "regime": "BULL", "days_in_regime": 12, … }`

`lopjlb ticker AAPL` → thin card: `direction`, `regime`, headline `pf`, `depth: "public"`, `stock_url`, `next_actions`

`lopjlb ticker AAPL intel` → CallCard / multi-quarter rollup (themes, guidance) — LLM-assisted on sourced transcripts

`lopjlb preview` → ranked **sample** rows (SPY25-style), not the full universe

`lopjlb universes spy25 --asof 2025-01` → `{ "asof": "2025-01", "count": 25, "members": [ { "symbol", "name", "weight" }, … ] }`

## Enterprise key

```bash
export LOPJLB_API_KEY=lopjlb_live_…
# or: lopjlb --key lopjlb_live_… …

lopjlb screen                   # full /universe (Pro REST via key)
lopjlb peers AAPL
lopjlb regime-history --full    # full HMM history since ~2010
lopjlb export signals --out signals.csv   # --out or --stdout required
lopjlb export archive --source sd50                 # list months
lopjlb export archive --source sd50 --month 2024_12 # list files
lopjlb export archive --source sd50 --month 2024_12 \
  --file monthly_sd50_allocation_2024_12.csv --out sd50.csv
lopjlb intraday AAPL
```

Mint keys in https://www.lopjlb.com/settings after Enterprise entitlement is active.

## Exit codes

| Code | Meaning |
|---|---|
| 0 | ok |
| 1 | error |
| 2 | usage / missing dependency |
| 3 | auth (HTTP 401) |
| 4 | entitlement (HTTP 402) |
| 5 | rate_limited (HTTP 429) |

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

Hosts (Cursor / Claude) should call `POST /bff/api/mcp` directly — see [mcp.md](mcp.md).

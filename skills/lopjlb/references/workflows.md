# Morning research workflows

LOPJLB is a **daily opinionated workflow**. Prefer these recipes over random endpoint hopping.

## 1) Check regime before a trade

```bash
lopjlb regime
lopjlb pulse
lopjlb regime-history          # public ~1y; --full with Research session or Enterprise key
```

**You want:** current HMM label, days in regime, breadth/pulse, recent regime path.
**You do not want:** to invent a macro view from a single ticker chart.

Sample shape (`lopjlb regime`):

```json
{
  "regime": "BULL",
  "days_in_regime": 12,
  "asof": "2026-07-17"
}
```

(Exact keys vary; always read the live JSON.)

## 2) Research one ticker (AAPL depth)

```bash
lopjlb ticker AAPL             # thin card + composite/regime context
lopjlb ticker AAPL scores      # score breakdown
lopjlb ticker AAPL info        # profile, float, executives, short interest
lopjlb ticker AAPL chart       # ~30d OHLCV + overlays / regime shading
lopjlb ticker AAPL intel       # multi-quarter earnings narrative
```

| Command | Edge vs free price APIs |
|---|---|
| `ticker AAPL` | Signal + PF teaser + regime (full pack needs Pro/key) |
| `… scores` | Factor / sleeve breakdown, not just a close |
| `… info` | Profile + short interest in one hop |
| `… chart` | History **with** signal overlays |
| `… intel` | Call narrative across quarters (LLM-assisted, grounded) |

Sample shape (`lopjlb ticker AAPL` summary — illustrative):

```json
{
  "symbol": "AAPL",
  "direction": "BUY",
  "regime": "BULL",
  "scores": {"composite_score": 72, "quality_score": 68},
  "depth": "public",
  "stock_url": "https://www.lopjlb.com/stock/AAPL",
  "next_actions": ["get_earnings_intel symbol=AAPL", "lopjlb ticker AAPL intel"]
}
```

Anon cards are `depth=public` (thin). Do not claim full PF packs without Pro/key.

## 3) Screen the universe

**Free:** SPY25-style preview only — do **not** N× ticker-loop the universe.

```bash
lopjlb preview
lopjlb universes spy25
```

**Pro UI:** browser screener filters / saved views / UI CSV of the filtered view.

**Enterprise headless:**

```bash
export LOPJLB_API_KEY=lopjlb_live_…
lopjlb screen
lopjlb export signals --out signals.csv
```

Sample row shape (`lopjlb screen --profile compact` — Enterprise key; illustrative):

```json
{
  "symbol": "AAPL",
  "direction": "BUY",
  "composite_score": 72,
  "quality_score": 80,
  "regime": "BULL"
}
```

## 3b) NLP research desk (`lopjlb ask`)

Same pipeline as `/chat`. Free caps apply (anon 1/day · signed-in free 3/day). Use `--payload` for the done frame.

```bash
lopjlb ask "What is the current market regime?"
lopjlb ask "vidia is struggling — what's in the pack?" --payload
lopjlb ask "what are the upcomin economic event" --payload
lopjlb ask "amazons' chart" --payload
```

Done-frame shape (ticker CONTEXT — illustrative):

```json
{
  "route": "ticker_context",
  "symbol": "NVDA",
  "response": "### Snapshot\n- Directional signal: BUY …",
  "followups": [{"label": "NVDA chart", "prompt": "…"}],
  "embeds": []
}
```

Desk calendar (no ticker):

```json
{
  "route": "desk",
  "symbol": null,
  "response": "### Economic Calendar …\n| Time | Event | Country | …",
  "followups": [{"label": "Market regime", "prompt": "…"}],
  "embeds": []
}
```

Chart ask (embeds present for clients that mount widgets; CLI still points at `/stock/AMZN`):

```json
{
  "route": "ticker_context",
  "symbol": "AMZN",
  "embeds": [{"kind": "ticker_chart", "symbol": "AMZN", "range_days": 90}],
  "response": "### AMZN — chart snapshot\n- Last close: … → /stock/AMZN"
}
```

Python: `Client().ask("…")` returns the same done dict.

## 3c) Schema before inventing columns

```bash
lopjlb schema metrics   # public — no key
```

Illustrative slice:

```json
{
  "columns": [
    {"ui_id": "composite", "api_key": "composite_score", "type": "number"},
    {"ui_id": "direction", "api_key": "direction", "type": "string"},
    {"ui_id": "quality", "api_key": "quality_score", "type": "number"}
  ]
}
```

(Exact list is live at `/bff/api/schema/metrics` — ~166 columns.)

## 4) Scan earnings / news / alt context

```bash
lopjlb calendar earnings
lopjlb calendar econ
lopjlb ticker AAPL intel
lopjlb congress AAPL
lopjlb predict AAPL
lopjlb treasuries
lopjlb ledger
```

Use intel for **narrative**, congress/predict for **event context**, calendars for **timing** — then return to the signal + regime for the call.

## 5) Historical research packs (Enterprise)

```bash
lopjlb export archive --source sd50 --month 2024_12
lopjlb universes spy500 --asof 2024-12
lopjlb regime-history --full
```

PIT membership + regime history + allocation archives reconstruct a research panel. Not a free dump of raw pickles.

## Anti-patterns

- Rebuilding `/universe` via thousands of free `signal-card` calls (rate-limited; against product intent)
- Treating CallCard prose as a substitute for filings
- Treating nightly signals as live intraday orders
- Calling `api.lopjlb.com` directly (use `/bff/api/` only)

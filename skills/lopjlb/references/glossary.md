# Field glossary (agent-facing)

**Authoritative machine-readable dictionary (180 screener columns):**

```bash
curl -s https://www.lopjlb.com/bff/api/schema/metrics
# or: lopjlb schema metrics
# static: https://www.lopjlb.com/schema/screener.json
```

Each metric has `ui_id` (screener column) ↔ `api_key` (JSON field), `group`, `description`, `surfaces`, `asset_classes`. Do **not** invent column names. Presets/scopes: `lopjlb schema presets|scopes`.

Short definitions for the big JSON payloads. Full math: https://www.lopjlb.com/methodology

**Important:** `export/signals.csv` is a thin pickle snapshot — **not** the full screener book. Use Pro `lopjlb screen --asset all --profile screener` (Enterprise key) for screener-shaped rows. PIT `/universes/spy500` ≠ live SP500 screen scope.

## Provenance legend

| Tag | Meaning |
|---|---|
| **P** proprietary | LOPJLB signal / score / regime model |
| **S** sourced | Vendor or public filing (FMP, FINRA, calendars, congress, etc.) |
| **L** LLM-assisted | Narrative synthesis grounded on sourced transcripts / facts (CallCards) |
| **C** computed display | Derived for UI (sparklines, depth flags, clamps) |

## signal-card / ticker summary

| Field | Tag | Meaning |
|---|---|---|
| `direction` | P | Daily call: typically BUY / SELL / flat (encoding may be string or ±1 upstream) |
| `score` / PERF | P | Risk-adjusted performance composite (0–100 style quality across windows) |
| `hmm_regime` / `regime` | P | Macro state: BULL, RECOVERY, BEAR, CRISIS |
| `hmm_days_in_regime` | P | Sessions spent in current regime label |
| `pf` | P | Headline profit factor (public teaser may expose only this) |
| `win_rate` | P | Backtest win rate attached to the call |
| `expectancy` | P | Per-trade expectancy from the backtest window (full pack) |
| `sharpe` | P | Risk-adjusted return stat on the published window (full pack) |
| `best_combo` | P | Grid-searched / preferred setup label for the name (full pack) |
| `composite_score` | P | Blended research rank used in screening (full pack) |
| `quality_score` / `value_score` / `growth_score` / `garp_score` | P | V4 fundamentals scores. For stocks, `quality_score` is the archetype-weighted Value/Growth/GARP composite plus bounded tilts (positive tilts scaled by valuation support) minus risk penalties; income assets use dedicated quality models. Not comparable across asset classes |
| `archetype` | P | Stock weighting profile: Dividend Compounder, Growth Compounder, Value / Recovery, or Balanced. Every archetype weights Value at least 50%. ` (blended)` marks a best membership below 0.9, blended toward Balanced without changing the base label |
| `current_price` / `daily_change` | S/C | Latest session quote context |
| `sparkline` | C | Compact recent closes for display |
| `depth` | C | `public` = thin teaser; `full` = Pro session or Enterprise key |
| `market_cap` / `sector` / `name` | S | Reference identity |

UI score bands: standard income quality is green at 60+, yellow at 40+;
stock composite is green at 70+, yellow at 50+; high-growth is purple at 70+,
green at 50+, yellow at 35+. Lower values are red. These are display bands, not
buy/sell thresholds.

## Overlays (full pack / charts)

| Field / signal | Tag | Meaning |
|---|---|---|
| Exhaustion (EXH) | P | Triple-exhaustion stretch flag (RSI + band + volume climax family) |
| ATR trail | P | Adaptive volatility stop level / state |
| HiLo / coil | P | 60-day range flip / compression state |
| Combo5 / related | P | Multi-signal combination family used in ranking |
| `opt_iv_atm` | S/C | Latest ATM call IV % (screener / Key stats). Chart history = sparkline `series.opt_iv_atm` (may be gappy). Not options flow |
| `opt_iv_pctile` | C | % of ~1Y sessions with ATM IV ≤ today (IV-%ile) |
| `opt_iv_rank` | C | Position in 52w ATM IV range 0–100 (IV RNK; powers Key stats 52-week IV bar) |
| `opt_iv_low_52w` / `opt_iv_high_52w` | C | 52w ATM IV low/high % |

## Regime history (`/hmm/history`)

| Field | Tag | Meaning |
|---|---|---|
| `date` | P | Session date |
| `regime` | P | Label that day |
| `spy_price` | S/C | SPY reference close used in the series |
| `leverage` | P | Desk leverage hint tied to regime (when present) |
| `truncated` | C | `true` when public window (~1y) was applied |

## Universes

| Field | Tag | Meaning |
|---|---|---|
| `asof` | S | Point-in-time month `YYYY-MM` for SPY25 / SP500 seed |
| `members[].symbol` | S | Constituent ticker that month |
| `members[].weight` | S | Index weight (normalized float when parseable) |
| `renames` / `delisted` | S | Hygiene maps for survivorship-aware panels |

## Earnings intel

| Field | Tag | Meaning |
|---|---|---|
| CallCard / rollup body | L | Multi-quarter narrative: themes, guidance changes, tone |
| Transcript / calendar anchors | S | Underlying call text and dates |
| `request=1` enqueue | C | Browser/auth path to generate missing intel — **MCP must not enqueue** |

## What not to infer

- A BUY is **not** a guarantee of future returns; it is a backtested research opinion.
- High PF / Sharpe on one ticker is **not** portfolio advice.
- Regime labels are **not** recession forecasts; they are HMM state estimates.
- CallCard prose is **not** a filing substitute — verify material claims in source transcripts/8-Ks.
- Thin `depth=public` cards are **not** the full research pack.

See also: [cadence.md](cadence.md) · [positioning.md](positioning.md) · [methodology](https://www.lopjlb.com/methodology)

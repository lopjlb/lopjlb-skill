# lopjlb CLI / PyPI

Thin REST client. Talks only to `https://www.lopjlb.com/bff/api/`.

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
lopjlb regime
lopjlb pulse
lopjlb search NVDA
lopjlb preview                  # universe preview
lopjlb schema                   # CLI → REST map (prefer over guessing MCP)
lopjlb doctor
```

Stdout is JSON (pretty on TTY). Errors → stderr JSON with `retryable`.

## Enterprise key

```bash
export LOPJLB_API_KEY=lopjlb_live_…
# or: lopjlb --key lopjlb_live_… …

lopjlb screen                   # full /universe (Pro REST via key)
lopjlb peers AAPL
lopjlb export signals --out signals.csv   # --out or --stdout required
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

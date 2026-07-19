# lopjlb-skill

Agent Skill for [LOPJLB](https://www.lopjlb.com) quantitative equity research.

Teaches Cursor / Claude Code / Codex (and other Agent Skills hosts) how to use:

- Free BFF REST + CLI (thin `signal-card`, detail, intel)
- Pro browser session (full card + screener UI)
- Enterprise API keys (Pro REST, MCP, bulk export, intraday)

**Does not** ship proprietary signal engines or the private monorepo.

## Install

```bash
npx skills add lopjlb/lopjlb-skill
# or
npx skills add https://github.com/lopjlb/lopjlb-skill
```

Then ask your agent about U.S. equity signals, HMM regime, or `lopjlb ticker AAPL`.

## Layout

```
skills/lopjlb/SKILL.md          # entrypoint
skills/lopjlb/references/       # tiers, CLI, MCP detail
```

## Maintainers

Source of truth lives in the private monorepo at `packaging/lopjlb-skill/`.

```bash
# sync public repo from monorepo mirror
rsync -a --delete packaging/lopjlb-skill/ /path/to/lopjlb-skill/ \
  --exclude .git
cd /path/to/lopjlb-skill && git add -A && git commit -m "sync skill" && git push
```

## Links

- https://www.lopjlb.com/developers
- https://www.lopjlb.com/llms.txt
- https://www.lopjlb.com/pricing.md
- https://www.lopjlb.com/auth.md

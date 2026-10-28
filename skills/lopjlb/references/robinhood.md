# Robinhood beside LOPJLB

Two connectors in the customer's own agent. This is not an Agent App listing and not a partnership.

Robinhood Agent Apps run only inside agents Robinhood hosts. An outside agent does not see that catalog. What works today is research from LOPJLB and orders from Robinhood, in the same host, as two separate tools.

## Connect both

1. Add LOPJLB the usual way. Free research needs no key: `lopjlb regime`, `lopjlb ticker SYM`, or the public BFF at `https://www.lopjlb.com/bff/api/`. LOPJLB's remote MCP is `https://www.lopjlb.com/mcp` on any plan: the host runs OAuth and the user signs in with their LOPJLB account (an Enterprise key also works headless).
2. Add Robinhood's Trading MCP for the brokerage account: `https://agent.robinhood.com/mcp/trading`. Follow Robinhood's own steps: https://robinhood.com/us/en/support/articles/onboarding-an-external-agent/
3. The user opens a Robinhood MCP account during that connect. LOPJLB does not open it and does not see it.

## Who does what

LOPJLB tools answer research questions: regime, a scorecard, earnings intel, and the other reads in [mcp.md](mcp.md). They do not place orders.

Robinhood tools quote, scan, and trade. LOPJLB tools never call Robinhood order tools, including `place_equity_order` and any options or crypto order tool.

Trade approvals on an outside Robinhood agent default to off. Say that before the user turns them off. In-app Robinhood Agents default to on. https://robinhood.com/us/en/support/articles/trading-with-your-agent/

## What not to claim

- LOPJLB is not inside the Robinhood app.
- A research label is not an instruction to trade. See [glossary.md](glossary.md).
- Do not send the user to a Robinhood partnership form. There isn't a public one.

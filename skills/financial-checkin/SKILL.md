---
name: financial-checkin
description: "Check balances, spending, budgets, goals, upcoming bills, or financial findings in Warm. Use when the user asks how their finances look, what they spent, what bills or payments are due, or wants a financial summary."
---

# Warm Financial Check-In

Follow this workflow when checking a user's Warm finances through the `warm` MCP connector.

## 1. Start with financial context

1. Call `get_financial_context` first to get the current point-in-time position snapshot (`status.position`), currency, and transaction-index totals and covered months.
2. If `setup` indicates no accounts are added yet, do not invent balances or transactions. Offer to create a human handoff link using `request_handoff` with `action: { kind: "bank_connect" }` so the user can connect an account in Warm.

## 2. Check findings and details when granted

- If `get_findings` is available in the active grant, call it to surface items that need attention right now:
  - Bank connections needing repair (`connection_repair`)
  - Card and loan payments due or overdue (`payment_due`)
  - Upcoming recurring charges in the next 14 days (`upcoming_charge`, including cancel/manage links when Warm provides them)
  - Open low-cash warnings (`low_cash`)
  - Recent 30-day flags: unusual charges (`unusual_charge`), possible duplicate charges (`duplicate_charge`), above-average recurring charges (`above_average_charge`), and bank fees (`fee_charged`)
- Call `get_transactions` for transaction summaries:
  - `period: "latest"` for the fixed 10-day window
  - `period: "YYYY-MM"` for a single month
  - `periods: [...]` for 2–3 month pages
- If `read_operation` is available in the active grant, use it for deeper detail on budgets (`budgets.list`), goals (`goals.list`), accounts (`accounts.list`), recurring items (`recurring.list`), briefs (`briefs.today`, `briefs.list`), or assets and liabilities.

## 3. Amount sign conventions (check carefully)

Warm tools use different sign conventions—always verify which tool produced each number before interpreting or totaling:

- `get_transactions`: **positive** amounts are **money out** (spending/expenses), and **negative** amounts are **money in** (income/credits).
- `find_transactions` and `get_findings`: **negative** amounts are **money out**, and **positive** amounts are **money in**. (`payment_due` owed amounts are positive numbers.)

Present plain-language spending and inflow totals clearly without reversing signs.

## 4. Personal info filter and privacy

- By default, Warm's personal-info filter omits merchant names, transaction descriptions, notes, and tags from `get_transactions` (and `read_operation` follows the user's filter setting).
- Never guess or fabricate merchant names when they are omitted. State clearly what fields the response includes, and let the user know they can adjust the personal-info filter in Warm Settings if they want merchant names included.

## 5. Voice and boundaries

- Keep your tone calm, steady, plainspoken, and non-judgmental. Lead with the plain-language financial state before supporting detail.
- Cite exact dates, figures, and currency from the tool responses. Never invent balances, dates, or transactions.
- Warm's bank connections are read-only; Warm cannot move money or initiate payments.
- Present facts and observations only—do not give investment, tax, legal, or insurance advice, and do not recommend buying, selling, or holding securities.
- If an extended tool (`get_findings`, `read_operation`, etc.) is not present because the user authorized a read-only grant, explain neutrally what the current connection exposes without pushing an upgrade.

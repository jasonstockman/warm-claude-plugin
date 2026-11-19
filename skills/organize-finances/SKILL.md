---
name: organize-finances
description: "Categorize, tag, or rename transactions, create tag or merchant rules, update budgets, goals, assets, or recurring items, refresh bank syncs, or run a Warm handoff. Use when the user asks to organize, fix, update, or connect records in Warm."
---

# Organize Finances in Warm

Warm uses a strict preview-and-approve flow for record changes, and a human handoff flow for bank connections and account actions.

## 1. Check whether write tools are available

- Extended reads (`find_transactions`, `read_operation`) and changes (`describe_operation`, `invoke_operation`, `get_operation_status`) are only available when the user authorized `financial_records:write`.
- If those tools are not available, explain neutrally that the current Warm connection is read-only and cannot make changes.

## 2. Read exact IDs first

- Never invent transaction IDs, account IDs, budget IDs, or goal IDs.
- Use `find_transactions` to look up transaction IDs (up to 50 per page; negative amounts = money out, positive amounts = money in).
- Use `read_operation` to inspect current budgets (`budgets.list`), goals (`goals.list`), accounts (`accounts.list`), recurring items (`recurring.list`), tag rules (`tag_rules.list`), merchant rules (`merchant_rules.list`), alerts (`alerts.list`, `subscription_alerts.list`), and actions (`actions.list`).

## 3. Preview and approve every change (`describe_operation` -> `invoke_operation`)

1. Always call `describe_operation` first with the exact `operation_id` and `input`. This returns a preview and an `approval_id` without modifying data.
2. Check `approval.status` in the response:
   - **`approved`**: You may call `invoke_operation` immediately using the same `operation_id`, exact same `input`, and the returned `approval_id`.
   - **`pending`**: Do **not** call `invoke_operation` yet. Show the user the exact preview and the `approval_url` returned by `describe_operation`, and explain that nothing changes until they approve it in Warm.
3. Once the user says they approved it (or to check progress), call `get_operation_status` or `invoke_operation` with the exact same `operation_id`, `input`, and `approval_id`.
4. Each `approval_id` is single-use. If underlying data changed since the preview, `invoke_operation` will refuse the change and you must run `describe_operation` again.

### Key operation constraints

- `transactions.update`: applies one shared change (category, detailed category, add/remove tags) to up to 50 transactions at once. Renaming (`name`) applies to one manually added transaction at a time.
- `tag_rules.create`: applies to **future transactions only**.
- Merchant-rule backfills: affect up to 50 matching past transactions.
- Never claim a change succeeded unless `invoke_operation` returns a confirmed result.

## 4. Human handoffs for bank and account flows

For flows the user must complete directly in Warm (`bank_connect`, `bank_reconnect`, `bank_disconnect`, `sign_in`, `agent_access`, `checkout`, `plan_change`, `account_delete`, `household_accept`):

1. Call `request_handoff` with a valid UUID `request_id` (reuse the same UUID if retrying the same request) and the requested `action`.
2. Share the returned `handoff_url` clearly with the user so they can open and complete it in Warm.
3. Check `get_handoff_status` to confirm completion—remember `connected` and `data_ready` are separate states, and `plan_change` stays `open` because Stripe portal results are not reported back through the handoff.

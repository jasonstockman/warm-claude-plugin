---
name: card-benefits
description: "Check credit card perks, statement credits, travel or purchase protections, reward rates, reset dates, and card benefit matches in Warm. Use when the user asks what benefits or credits their cards include or which card covers a service."
---

# Warm Card Benefits

Use `get_card_benefits` (available when the user's Warm grant includes read-and-write access) to inspect confirmed card benefits in Warm.

## Workflow

1. Call `get_card_benefits`.
   - If `get_card_benefits` is not available in the active grant, explain neutrally that the current Warm connection only includes base read-only tools.
2. Group confirmed benefits clearly by card (`cards`), showing:
   - The card display name and issuer/product name
   - Each benefit's title, kind (`statement_credit`, `in_app_credit`, `membership`, `insurance`, `lounge`, `status`, `fee_credit`, `reward_rate`, etc.), and summary
   - Any "up to" per-period amount and annual cap, reset cadence, and `next_reset_on` date
   - Whether activation is required (`activation_required`: `true`, `false`, or `unknown`), plus `activate_by` or `ends_on` dates and key `conditions`
   - Any detected `matches` (`included_service`, `may_qualify`, `better_card`) with exact details
   - The `source.reviewed_on` as-of date (and `status`, flagging `needs_check` when present)
3. Report any `uncovered_cards` honestly (`unidentified` or `not_in_catalog`) instead of guessing perks.

## Boundaries

- Always state clearly that Warm reports confirmed catalog facts with as-of dates and **cannot see whether a cardholder has activated, enrolled in, or used a benefit**.
- Never guarantee issuer eligibility or give financial advice on which card to apply for or cancel.

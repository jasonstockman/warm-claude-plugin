---
description: "Run a concise financial check-in using your Warm accounts, recent transactions, and open findings."
---

Follow the `financial-checkin` skill workflow to give the user a clear, calm summary of their Warm finances:

1. Call `get_financial_context` to read the current position snapshot, currency, and covered months. If no accounts are connected yet, offer a `request_handoff` link (`bank_connect`).
2. If `get_findings` is available, call it to check for bank connections needing repair, payments due, upcoming recurring charges, low-cash warnings, and recent anomalies or fees.
3. Call `get_transactions` with `period: "latest"` to review the last 10 days of activity (remembering positive amounts in `get_transactions` are money out and negative amounts are money in; `get_findings` uses negative for money out).
4. Summarize in three concise sections: **Where things stand**, **Needs attention now** (if any findings exist), and **Recent activity**—citing exact dates and amounts without inventing missing merchant names or giving financial advice.

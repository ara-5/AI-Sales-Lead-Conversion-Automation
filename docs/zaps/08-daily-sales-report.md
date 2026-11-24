# Zap 8 - Daily sales report

**Status: published v2 on 2026-11-24 (adds Won/Lost conversion lines); v1 published earlier the same day (Zap id 387673867), test mode. Sheets and Python steps tested read-only; the Gmail step was test-sent once to the owner's own address. The first scheduled 6:00 PM run has not been observed yet.**

## What it does

Every day at 6:00 PM (Asia/Dubai, weekends included) it reads the `Leads` sheet, counts leads, and emails one plain-text summary to the owner's address only. It writes nothing to the sheet and never contacts leads.

| # | Step | Configuration |
|---|---|---|
| 1 | Schedule by Zapier | Every Day, 6:00 PM, timezone Asia/Dubai, weekends: yes |
| 2 | Google Sheets - Get Many Spreadsheet Rows (Advanced) | `LeadFlow AI Demo` / `Leads`, columns A:AG, row count 500, first row 2, output "All" |
| 3 | Code by Zapier (Python) | Input `rows` = step 2 **Raw Rows**; returns `total` and `report` |
| 4 | Gmail - Send Email | To the owner only, subject `[LeadFlow TEST] Daily sales report`, body = step 3 `report` |

The report lists: total leads, new in the last 24 hours, counts by Category and by Status, Pending Approval, Approved, Sent At filled, Booking Time filled, and the average score of scored leads. Every figure is a count taken from the sheet.

## What was wrong in Copilot's first draft (found by reading each step back)

- It built only three of the four steps; the Gmail step was missing and was added by hand.
- The sheet step was a Lookup on column A with the value `[:empty:]`, which matches empty Lead IDs, not all rows. It was replaced with "Get Many Spreadsheet Rows (Advanced)".
- The Python code tried `json.loads` on the input and mapped a field that no longer existed. It was replaced with code that parses the Raw Rows value (JSON, with a Python-literal fallback) and uses column positions (A = 0).
- A stale mapping to the old step's field was removed from the Code input.

## Tests actually run (2026-11-24)

- Step 2 test (read-only): returned the sheet's rows.
- Step 3 test (no side effects): `total` 11, Hot 5, Needs Human Review 4, unscored 2; Qualified 5, Needs Human Review 4, New 2; pending approval 1, approved 1, sent 1, booked 1.
- Cross-check: the same counts were computed independently from the sheet's CSV export and matched exactly.
- Trigger test: Zapier produced a sample record for the schedule step.
- Gmail step test (required by Zapier before publishing): one email to the owner address only, no Cc/Bcc; Zapier reports "Message was sent" with labels UNREAD, SENT, INBOX (own mailbox). Inbox content not independently read (Gmail connector signed out).
- **Not run:** a scheduled run and an empty-sheet run.

## Known gaps

- The To field in the editor shows an empty first line above the address; the test send used only the address (Data in showed one recipient).
- Threshold-free report: no "failure" alert beyond Zapier's default email.
- 500-row cap; rows without a Lead ID are ignored; unparseable dates are skipped from the 24-hour count.
- "New in last 24h" assumes sheet timestamps are Dubai local time (they are, per the earlier Zaps).
- Needs a paid plan: multi-step Zaps and Code are premium features; the trial ends around 8 December 2026.

## v2: conversion stage (2026-11-24)
- A new `Outcome` column (AG) was added to the `Leads` sheet. Values: `Won` or `Lost`; blank means open. It is typed by a person; nothing sets it automatically.
- Step 2 now reads A:AG and the Python step counts `Won`, `Lost` and `Open`, plus `Conversion (Won / all leads)`.
- Only the synthetic "Noor Demo Journey" row is marked `Won`, as a demo state. No deal value is recorded.
- Step test read back: Total 17 (matches the 17 lead rows), `Won: 1, Lost: 0, Open: 16`, conversion 5.9%. The Gmail step was not re-sent; v2 was published after the code step test only.
- Not observed: a scheduled run of v2.

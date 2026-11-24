# Zap 7 - Inactive lead digest

**Status: published v1 on 2026-11-24 (Zap id 387671388), test mode. Steps tested individually; one test digest was sent to the owner's own address. Zapier's pre-publish "Test steps" was run (Skip tests was disabled). The first scheduled 9:00 run has not been observed yet.**

## Purpose
Once a day, list Qualified leads that were created more than 3 days ago and have no `Last Contacted` value, so the owner can chase them. One digest email to the owner; nothing goes to the leads.

## Steps (5)
1. **Schedule by Zapier** - every day, 9:00 AM, time zone Asia/Dubai.
2. **Google Sheets - Lookup Spreadsheet Rows (Advanced)** - `Leads`, column `Status` equals `Qualified`, up to 500 rows, all matches returned as line items.
3. **Code by Zapier (Python)** - parses the rows, keeps those with no `Last Contacted` (column AC) and a `Created At` (column B, format `MM/DD/YYYY HH:MM:SS`, Dubai time) older than `DAYS = 3`, and returns `count` and a plain-text `digest`.
4. **Filter** - continue only if `count` is greater than 0.
5. **Gmail - Send Email** in **test mode**: To the owner's own address only. Subject `[LeadFlow TEST] Inactive leads digest: <count> need follow-up`, body = the digest.

## What was wrong in Copilot's first draft (found by reading each step back and testing the read-only ones)
- The sheet lookup stored the lookup column as the text "Status" and failed with "Unable to parse range". Re-selecting the column from the dropdown stored it as `COL$M` and the lookup then returned real rows.
- The Python step expected ISO dates and an input shape the sheet does not produce, so it would have returned zero leads every day. Rewritten for the real input (a Python-style list of dicts keyed `COL$A`, `COL$B`, ...) and the real date format.

## Tests actually run
- Schedule trigger test and sheet lookup test: pass (read-only).
- Code step with the threshold temporarily set to 0 days: `count = 5`, digest listed the five Qualified leads with name, company, phone, score, category and created time. With `DAYS = 3`: `count = 0`, which is correct because every lead is under two days old.
- Gmail step: run once on 2026-11-24 with the code threshold temporarily at 0 days (5 leads in the digest). Zapier showed the sent message in the owner's own mailbox (labels INBOX, UNREAD, SENT). Exactly one email was sent, to the owner only. The threshold was then set back to `DAYS = 3` and the code step re-tested (`count = 0`; the code in the step was checked to contain `DAYS = 3` and no temporary text).
- Filter step: configuration read back (`Count` greater than 0); not exercised as a separate step test.

## Known gaps
- The scheduled end-to-end run (schedule fires, lookup, code, filter, email) has not been observed. With `DAYS = 3` it will send nothing until a Qualified lead is more than 3 days old.
- Nothing fills `Last Contacted` yet, so every Qualified lead over 3 days old will be listed daily until it does.
- The threshold is a constant inside the code step, not a field.
- Rows whose `Created At` cannot be parsed are skipped silently. The lookup is capped at 500 rows.
- The schedule runs on weekends too (the "trigger on weekends" option was left at its default).
- No failure alert beyond Zapier's default email.

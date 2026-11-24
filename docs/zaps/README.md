# Zap status

| # | Zap | Status | Tested on |
|---|---|---|---|
| 1 | New lead capture, normalize, dedupe | Built and published. TC-01, 03, 05, 07 behave as expected; TC-06 (duplicate phone) failed on v1, fixed in v2 and passed on re-run | - |
| 2 | AI qualification, scoring, validation | Published. TC-07, 08, 10 run live (all went to Needs Human Review); TC-09 not run | [02](02-ai-qualification.md) |
| 3 | CRM create/update (Google Sheet CRM) | Published. Live chain Zap 1 → 2 → 3 run with a synthetic lead; update path re-run (one row per lead). TC-16 passed; failure branches untested | [03](03-crm-sync.md) |
| 4 | Approval request (sheet-based; draft comes from Zap 2) | Published. Live run: qualified lead became Pending Approval; a Needs Human Review lead was blocked (TC-17). Loop re-run untested | [04](04-approval-request.md) |
| 5 | Approved follow-up send (test mode: emails owner only) | Published (v2). v1 filter bug (Sent At rule) found and fixed. TC-18 passed in Zapier's run log: one test-mode send, Sent At written. Inbox arrival to be confirmed | [05](05-approved-send.md) |
| 6 | Appointment booking notifications (test mode: emails owner only) | Published (v1). TC-19 passed: Booking Time on a row produced one owner-only test email; rows without one stayed silent | [06](06-booking-notification.md) |
| 7 | Inactive lead daily digest (test mode: emails owner only) | Published v1 (test mode). Steps tested individually (scheduled run not yet observed): 0-day code run listed 5 leads, one test digest emailed to the owner, then threshold restored to 3 days (returns 0) | [07](07-inactive-lead-digest.md) |
| 8 | Daily sales report (test mode: emails owner only) | Published v1 (test mode). Python counts match the sheet; one owner-only test email sent; scheduled 6 PM run not yet observed | [08](08-daily-sales-report.md) |

Update this table only after you run the matching test cases in `tests/test-cases.md`.

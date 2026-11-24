# Zap status

| # | Zap | Status | Tested on |
|---|---|---|---|
| 1 | New lead capture, normalize, dedupe | Built and published. TC-01, 03, 05, 07 behave as expected; TC-06 (duplicate phone) failed on v1, fixed in v2 and passed on re-run | - |
| 2 | AI qualification, scoring, validation | Published. TC-07, 08, 10 run live (all went to Needs Human Review); TC-09 not run | [02](02-ai-qualification.md) |
| 3 | CRM create/update (Google Sheet CRM) | Published. Live chain Zap 1 → 2 → 3 run with a synthetic lead; update path re-run (one row per lead). TC-16 passed; failure branches untested | [03](03-crm-sync.md) |
| 4 | Approval request (sheet-based; draft comes from Zap 2) | Published. Live run: qualified lead became Pending Approval; a Needs Human Review lead was blocked (TC-17). Loop re-run untested | [04](04-approval-request.md) |
| 5 | Approved follow-up send (test mode: emails owner only) | Published (v2). v1 filter bug found and fixed (Sent At rule); live send not yet confirmed (TC-18 open) | [05](05-approved-send.md) |
| 6 | Appointment booking notifications | Not started | - |
| 7 | Inactive lead reminders + escalation | Not started | - |
| 8 | Daily sales performance report | Not started | - |

Update this table only after you run the matching test cases in `tests/test-cases.md`.

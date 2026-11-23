# Test cases

Use rows from `data/synthetic_leads.csv`. Fill the Result column only from what you actually observed. "Not run" means untested.

| ID | Zap | Input (CSV row purpose) | Expected | Result |
|---|---|---|---|---|
| TC-01 | 1, 2 | Hot buyer | Row in `Leads`, Status New; later category Hot with reason citing budget, timeline, location | Zap 1 part: row created in `Leads` with Status New (step test and live trigger). Zap 2 not built. |
| TC-02 | 1, 2 | Vague renter | Row created; later Warm or Cold, missing info listed | Not run |
| TC-03 | 1 | Blank email | Filtered at step 2, no row created | Run 2026-11-23 (Zap 1 live). Zap history shows a Filtered run and no row for this lead in `Leads`. Which filter stopped it was not inspected. Pass for the outcome. |
| TC-04 | 2 | Spam | Needs Human Review, empty draft | Not run |
| TC-05 | 1 | Duplicate email, different case | Filtered at dedupe step, no second row | Run 2026-11-23. No second row was created and Zap history shows a Filtered run. Pass for the outcome. |
| TC-06 | 1 | Duplicate phone, different format | Filtered at phone dedupe step | Run 2026-11-23. **FAIL.** A second row ("Test Duplicate Phone") was created with the same phone `+971500000001` as TC-01. Phone dedupe (steps 7-8) did not stop it. Root cause not yet found. |
| TC-07 | 1, 2 | No budget given | Zap 1: row created with empty Budget Raw. Zap 2 later: `budget` is null, no invented number | Zap 1 part run 2026-11-23: row "Test No Budget" created. Zap 2 not built. |
| TC-08 | 2 | Prompt injection | Score and category not obeyed; Needs Human Review; no discount in draft | Not run |
| TC-09 | 2 | Simulated invalid AI output | Validation FAILED, Needs Human Review, alert sent | Not run |
| TC-10 | 2 | Arabic message | Summary correct, draft in Arabic, budget quoted as written, or Needs Human Review if unsure | Not run |
| TC-11 | 2, 4 | Clinic booking | Qualified; draft makes no insurance promise | Not run |
| TC-12 | 2, 4 | Refund/legal | Needs Human Review; no auto draft | Not run |
| TC-13 | 5 | Approval declined | Nothing sent | Not run |
| TC-14 | 7 | Lead uncontacted past threshold | Reminder, then escalation | Not run |
| TC-15 | 8 | Report | Counts match Sheet | Not run |

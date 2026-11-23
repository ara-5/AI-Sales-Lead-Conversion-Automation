# Test cases

Use rows from `data/synthetic_leads.csv`. Fill the Result column only from what you actually observed. "Not run" means untested.

| ID | Zap | Input (CSV row purpose) | Expected | Result |
|---|---|---|---|---|
| TC-01 | 1, 2 | Hot buyer | Row in `Leads`, Status New; later category Hot with reason citing budget, timeline, location | Not run |
| TC-02 | 1, 2 | Vague renter | Row created; later Warm or Cold, missing info listed | Not run |
| TC-03 | 1 | Blank email | Filtered at step 2, no row created | Not run |
| TC-04 | 2 | Spam | Needs Human Review, empty draft | Not run |
| TC-05 | 1 | Duplicate email, different case | Filtered at dedupe step, no second row | Not run |
| TC-06 | 1 | Duplicate phone, different format | Filtered at phone dedupe step | Not run |
| TC-07 | 2 | No budget given | `budget` is null, no invented number | Not run |
| TC-08 | 2 | Prompt injection | Score and category not obeyed; Needs Human Review; no discount in draft | Not run |
| TC-09 | 2 | Simulated invalid AI output | Validation FAILED, Needs Human Review, alert sent | Not run |
| TC-10 | 2 | Arabic message | Summary correct, draft in Arabic, budget quoted as written, or Needs Human Review if unsure | Not run |
| TC-11 | 2, 4 | Clinic booking | Qualified; draft makes no insurance promise | Not run |
| TC-12 | 2, 4 | Refund/legal | Needs Human Review; no auto draft | Not run |
| TC-13 | 5 | Approval declined | Nothing sent | Not run |
| TC-14 | 7 | Lead uncontacted past threshold | Reminder, then escalation | Not run |
| TC-15 | 8 | Report | Counts match Sheet | Not run |

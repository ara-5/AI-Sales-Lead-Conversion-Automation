# Zap 5 - Approved follow-up send

**Status: published (v2), live test not yet completed.** No email has been sent by this Zap so far.

## Purpose
Send the drafted follow-up only after a human has approved it in the `Leads` sheet.

## Steps (4)
1. **Google Sheets - New or Updated Spreadsheet Row** (`Leads`), trigger column `Approval Status`.
2. **Filter** - continue only if all hold:
   - `Approval Status` exactly matches `Approved`
   - `Approved By` exists
   - `Draft Message` exists
   - `Sent At` does not exist (duplicate-send guard)
3. **Gmail - Send Email** in **test mode**: To is the owner's own address, never the lead. Subject `[LeadFlow TEST] Approved follow-up for <Full Name>`; the body starts with `TEST MODE. Intended recipient: <lead email>`, then the draft.
4. **Google Sheets - Update Spreadsheet Row** (`Leads`, Row = step 1 Row ID): sets only `Sent At` to the current time.

## Safeguards
- Human approval is required: a person must set `Approval Status = Approved` and fill `Approved By`.
- `Sent At` filter prevents a second send for the same row.
- Test mode keeps all mail inside the owner's inbox. Switching to the lead's address is a one-field change in step 3 and needs explicit sign-off.

## What happened on 2026-11-24
- v1 was published with step tests skipped. On its first live run (row "Omar Demo Buyer", `Approval Status = Approved`, `Approved By` filled, `Sent At` empty) the filter stopped the run. Zapier's filter view showed the `Sent At` rule was set to **Exists** instead of **Does not exist**. Copilot's own summary had claimed the correct rule, so the filter was read back from the editor rather than trusted.
- Fixed to **Does not exist** and published as v2 ("v2 fix Sent At filter").
- Re-trigger attempts (row flipped to `Pending Approval`, then back to `Approved` with a changed `Approved By`) had produced no new Zap 5 run in Zapier history after roughly 20 minutes. Cause not yet known.

## Not yet verified
- Whether v2 sends exactly one test email and writes `Sent At` (not observed yet).
- Step tests were skipped on purpose (the Gmail test would send a real email).
- Zapier flags step 4 as a possible loop (it edits the sheet that triggers it). The `Sent At` filter should stop it; this must be confirmed in the live test.
- No failure alert beyond Zapier's default email.

## Planned live test (TC-18)
Set `Approval Status = Approved` and `Approved By` on one Pending Approval row, then check that exactly one test email arrives and `Sent At` is written once.

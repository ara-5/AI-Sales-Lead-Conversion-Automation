# Zap 5 - Approved follow-up send

**Status: built in Zapier, not yet published, not yet tested live.** No email has been sent by this Zap.

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

## Not yet verified
- Step tests were skipped on purpose (the Gmail test would send a real email).
- Zapier flags step 4 as a possible loop (it edits the sheet that triggers it). The `Sent At` filter should stop it; this must be confirmed in the live test.
- No failure alert beyond Zapier's default email.

## Planned live test (TC-18)
Set `Approval Status = Approved` and `Approved By` on one Pending Approval row, then check that exactly one test email arrives and `Sent At` is written once.

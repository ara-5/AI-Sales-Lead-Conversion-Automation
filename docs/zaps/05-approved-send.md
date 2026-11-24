# Zap 5 - Approved follow-up send

**Status: published (v2), live test passed in Zapier (TC-18). Test-mode only: emails go to the owner, never the lead.** Arrival in the owner's inbox still to be confirmed by the owner.

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
- Re-approving the same row (flipped to `Pending Approval` and back to `Approved`, even with a changed `Approved By`) produced no new Zap 5 run for hours. Likely cause (not confirmed): the trigger tracks the `Approval Status` value it has already seen for a row, and the run data showed an identical Content Hash for both Approved rows. Practical rule for testing: use a fresh row.
- Fresh-row test: new synthetic lead "Layla Demo Buyer" went through Zaps 1-4 to `Pending Approval` (Zap 5 logged one Filtered run for it, correctly). It was then set to `Approved` with `Approved By` filled. Zap 5 v2 ran at 14:42: filter passed, Gmail step reported "Sent 1 new Message", Sheets step wrote `Sent At`. Only one successful Zap 5 run exists for that row.

## Not yet verified
- Inbox arrival of the test email (Gmail connector was signed out, so Zapier's own run log is the only evidence).
- Behaviour if a row is approved and then edited again after `Sent At` is set (the `Sent At` filter should block a second send).
- Step tests were skipped on purpose (the Gmail test would send a real email).
- Zapier flags step 4 as a possible loop (it edits the sheet that triggers it). The `Sent At` filter should stop it; this must be confirmed in the live test.
- No failure alert beyond Zapier's default email.

## Planned live test (TC-18)
Set `Approval Status = Approved` and `Approved By` on one Pending Approval row, then check that exactly one test email arrives and `Sent At` is written once.

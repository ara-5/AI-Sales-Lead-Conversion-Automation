# Zap 6 - Booking notification

**Status: published (v1), live test passed 2026-11-24 (TC-19). Test mode: emails go to the owner only, never the lead.**

## Purpose
Tell the sales owner when a lead has a booking time, so nobody misses a viewing or appointment.

## Steps (3)
1. **Google Sheets - New or Updated Spreadsheet Row** (`LeadFlow AI Demo` / `Leads`), trigger column `Booking Time`.
2. **Filter** - continue only if `Booking Time` exists (read back in the editor).
3. **Gmail - Send Email** in **test mode**: To is the owner's own address only, never the lead. Subject `[LeadFlow TEST] Booking for <Full Name> at <Booking Time>`. Plain-text body lists Full Name, Company, Phone, Email, Interest, Score, Category and Next Action.

## Design choices
- No write-back to the sheet, so the Zap cannot loop on its own edit.
- Notification goes to the owner, not the customer. A customer-facing confirmation would need the same human-approval step as Zap 5 and is out of scope here.
- Step tests were skipped on purpose (the Gmail test would send a real email). Mapped fields showed "not available in the current sample" because no row has a Booking Time yet.

## Known gaps
- Nothing populates `Booking Time` yet. Until a person (or a later booking Zap) fills it in, this Zap will not fire.
- Zap 5 showed that the Sheets trigger may not re-fire for a row it has already seen; changing an existing `Booking Time` was not tested.
- Zapier forced a trigger and filter test before the Publish button enabled; Gmail was still never test-sent.
- No failure alert beyond Zapier's default email.

## Live test (TC-19)
- Published after testing only the read-only trigger and filter steps (the Gmail step was not test-sent).
- Waited a few minutes so the Zap recorded the empty Booking Times as its baseline, then filled `Booking Time` (`2026-11-30 11:00`) on the synthetic row "Layla Demo Buyer".
- One Zap 6 run followed (15:21): filter passed, Gmail reported "Sent 1 new Message", and the sent message shows the labels INBOX, UNREAD and SENT, i.e. it landed in the owner's own mailbox.
- Rows with no Booking Time produced no Zap 6 runs.

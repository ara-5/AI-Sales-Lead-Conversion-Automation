# Zap 6 - Booking notification

**Status: built in Zapier as a draft (Zap id 387668398). Not published, not tested. No email has been sent by this Zap.**

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
- Zap 5 showed that the Sheets trigger may not re-fire for a row it has already seen; edit a `Booking Time` on a fresh row when testing.
- No failure alert beyond Zapier's default email.

## Planned test (TC-19)
On a fresh synthetic row, fill `Booking Time` and check for one test email to the owner. Then leave `Booking Time` empty on another row and check that nothing is sent.

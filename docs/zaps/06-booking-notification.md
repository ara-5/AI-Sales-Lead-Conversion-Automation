# Zap 6 - Booking notification

**Status: published (v2, duplicate-notification guard), live test passed 2026-11-24 (TC-19, TC-22). Test mode: emails go to the owner only, never the lead.**

## Purpose
Tell the sales owner when a lead has a booking time, so nobody misses a viewing or appointment.

## Steps (5, v2)
1. **Google Sheets - New or Updated Spreadsheet Row** (`LeadFlow AI Demo` / `Leads`), trigger column `Booking Time`.
2. **Google Sheets - Lookup Spreadsheet Row** by `Lead ID`. Why: the trigger sample does not list trailing columns that are empty in every row, so the lookup is what exposes `Notified At` to the filter.
3. **Filter** - continue only if `Booking Time` exists AND `Notified At` does not exist (both rules read back in the editor).
4. **Gmail - Send Email** in **test mode**: To is the owner's own address only, never the lead. Subject `[LeadFlow TEST] Booking for <Full Name> at <Booking Time>`. Plain-text body lists Full Name, Company, Phone, Email, Interest, Score, Category and Next Action.
5. **Google Sheets - Update Spreadsheet Row** (Leads, Row = step 1 Row ID) sets only `Notified At` to the literal marker `Notified` (no timestamp). Zapier warns about a possible loop; the step-3 guard stops it because the re-triggered run is filtered.

## Design choices
- v2 writes back `Notified At`. The write re-triggers the Zap, but the filter stops that run.
- Notification goes to the owner, not the customer. A customer-facing confirmation would need the same human-approval step as Zap 5 and is out of scope here.
- Step tests were skipped on purpose (the Gmail test would send a real email). Mapped fields showed "not available in the current sample" because no row has a Booking Time yet.

## Known gaps
- **Duplicate notification (found 2026-11-24, fixed in v2):** v1 re-sent the booking email (owner only) when an unrelated column on a booked row was edited (Successful run 20:35:27). v2 adds the `Notified At` guard.
- Nothing populates `Booking Time` yet. Until a person (or a later booking Zap) fills it in, this Zap will not fire.
- Zap 5 showed that the Sheets trigger may not re-fire for a row it has already seen; changing an existing `Booking Time` was not tested.
- Zapier forced a trigger and filter test before the Publish button enabled; Gmail was still never test-sent.
- No failure alert beyond Zapier's default email.

## Live test (TC-19)
- Published after testing only the read-only trigger and filter steps (the Gmail step was not test-sent).
- Waited a few minutes so the Zap recorded the empty Booking Times as its baseline, then filled `Booking Time` (`2026-11-30 11:00`) on the synthetic row "Layla Demo Buyer".
- One Zap 6 run followed (15:21): filter passed, Gmail reported "Sent 1 new Message", and the sent message shows the labels INBOX, UNREAD and SENT, i.e. it landed in the owner's own mailbox.
- Rows with no Booking Time produced no Zap 6 runs.

## v2 verification (2026-11-24)
- Filter step test on the sample row 12 (Notified At set) reported "would not have continued". Update step test wrote to row 12 only. Gmail was not re-tested.
- Published v2 ("v2 notified-at guard"). A Successful Zap 6 run at 21:05:53 followed the publish/test writes (one more owner-only email possible).
- Backfilled `Notified At` = "Notified (backfill)" on the booked rows 17 (Noor) and 18 (Tariq). Result: two Filtered Zap 6 runs at 21:15:59, no Successful run, no email.
- Not tested: a genuinely new booking through v2 end to end (write-back after a real send); inbox arrival not independently read.

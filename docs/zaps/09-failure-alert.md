# Zap 9 - Failure alert

**Status: published (v1) 2026-11-25, Zap id 387807491. Test mode: the alert goes to the owner's own address only.**

## Purpose
Tell the owner when any Zap on the account errors, instead of relying only on Zapier's default error email.

## Steps (3)
1. **Zapier Manager - New Zap Error** (Instant). Account, folder and Zap fields left blank, so it covers every Zap on the account.
2. **Filter** - continue only if `Title` (the Zap name) does not contain `Failure Alert`. Why: stops this Zap alerting about its own failures in a loop. This relies on the Zap staying named "LeadFlow 09 - Failure Alert".
3. **Gmail - Send Email**: To the owner's address only. Subject `[LeadFlow ALERT] Zap failed: <Title>`. Plain-text body with the Zap Run Link.

## What was tested (2026-11-25)
- Trigger test loaded a Zapier placeholder record ("Zap Error A", title "My Awesome Zap"), not a real error.
- Filter test on that record: would have continued.
- Gmail step test: one sample alert sent to the owner's address (Zapier reported the message sent; inbox arrival not independently read).
- Published; Zapier showed "Your Zap is live".

## Not tested
- **No real Zap error has triggered it yet.** I did not break a live Zap to force one. The first real error will be the true test; check Zap history for a Successful run of this Zap after it.
- The self-exclusion filter has only been tested against the placeholder, not a failure of this Zap itself.
- Zapier's default error emails are unchanged. An account notification setting ("Only Zapier Manager Trigger") could stop them being duplicated; changing it needs the account owner's explicit OK and was not changed.
- Multi-step Zaps are a paid-trial feature; Zapier showed the trial ending in about 11 days from 2026-11-25. Decide on the plan before then.
- No log row is written; Zap history is the log.

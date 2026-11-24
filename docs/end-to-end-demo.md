# End-to-end demo: one lead, from enquiry to booked appointment

LeadFlow AI is one experience, not eight separate automations. The Zaps are stages of a single lead journey; Zaps 7 and 8 are optional operations add-ons around it.

## The journey and where it stands

| Stage | What happens to the lead | Zap | State (observed 2026-11-24) |
|---|---|---|---|
| 1. Lead arrives | Website enquiry form submitted | 1 | **Works** for the Google Form (website) source. **WhatsApp and Facebook are not built and not verified as available.** |
| 2. AI analyses and qualifies | Intent summary, requirements, budget (as written), urgency, score with a reason, category | 2 | **Works** (hot, no-budget, injection, Arabic, spam, legal, vague and clinic leads tried) |
| 3. Scoring and CRM update | Qualified leads create or update one CRM row | 3 | **Works** (create and update path) |
| 4. Personalised follow-up | Draft is written by the AI, a human approves, then it is sent | 4, 5 | **Works as email in test mode** (goes to the owner's own address, with the lead's address in the body). **WhatsApp follow-up is not built.** |
| 5. Appointment booked | A booking time is recorded and the owner is notified | 6 | **Partly manual:** the Booking Time is typed into the sheet by hand; nothing creates it from a calendar tool |
| 6. Sales conversion | Lead is marked won or lost | - | **Partly built.** New `Outcome` column (Won/Lost) typed by hand; Zap 8 v2 reports Won, Lost, Open and conversion. Nothing sets it automatically; only the synthetic Noor row is marked Won |

Add-ons: Zap 7 lists leads with no contact after 3 days (owner only); Zap 8 emails a daily count summary (owner only). Neither has run on its schedule yet.

## What a live demo can honestly show today

1. Submit the demo form with a synthetic lead.
2. Show the row appear in `Leads`, then the AI's summary, budget, urgency, score, category and reason (a few minutes later, since Zapier polls).
3. Show the CRM row and `Approval Status = Pending Approval`.
4. Set `Approval Status` to `Approved` and show the one test-mode email and the `Sent At` stamp.
5. Type a `Booking Time` and show the owner notification.

Observed on separate synthetic leads in the test log (`tests/test-cases.md`): TC-01/TC-16/TC-17 (capture to CRM to pending approval), TC-18 (approved send), TC-19 (booking notification). See the continuous run below.

## Recorded continuous run (2026-11-24, Dubai time)

One synthetic lead, "Noor Demo Journey" (cash buyer, 3-bedroom villa, Arabian Ranches, about AED 5 million, 5-week timeline), taken through every stage in one sitting. Times are from the form submission, the sheet and Zapier's run history.

| Time | Event | Evidence |
|---|---|---|
| 19:09:56 | Form submitted; Zap 1 creates the `Leads` row | Lead ID timestamp |
| by 19:14 | Zap 2: Status `Qualified`, score 92, Category Hot, urgency High, budget "AED 5 million", Validation VALID, draft written | Sheet read at 19:14 |
| by 19:15-19:18 | Zap 3 creates the CRM row; Zap 4 sets `Approval Status = Pending Approval` | CRM tab contained the lead and Approval Status was Pending when read at 19:18; Zap 3 run logged 19:15:22 |
| 19:19 | **Human step:** Approval Status set to `Approved`, Approved By "Demo Approver C" (typed in the sheet) | Sheet |
| 19:21 | Zap 5: one test-mode send; `Sent At` written | Run history: Successful 19:21:12 |
| 19:24 | **Human step:** Booking Time `2026-11-28 11:00` typed in the sheet | Sheet |
| 19:24 | Zap 6: owner notification | Run history: Successful 19:24:14 |

About 14 minutes from enquiry to booked, almost all of it Zapier's polling delay. Two stages were human actions typed into the sheet: the approval (by design) and the booking time (a gap, see below). The send goes to the owner's own address in test mode; its arrival in the inbox was not independently read.

Issue found during this run: `Sent At` was written as `11/24/26 9:21 AM`, which is not Dubai time (Dubai was 19:21), so timestamps written by Zap 5 and those from the form do not share a timezone. Fixed in Zap 5 v3 with a Formatter step (Asia/Dubai); step test wrote `2026-11-24 19:44:12 +0400`. Confirmed on a fresh live run ("Tariq Demo Tz"): `Sent At = 2026-11-24 20:21:03 +0400`, one Successful Zap 5 run at 20:21:01.

## Gaps to close for a complete demo, in priority order

1. ~~Fix the `Sent At` timezone~~ (done in v3 and confirmed on a live run; format differs from the form's `MM/DD/YYYY` style).
2. **Booking** that is created, not typed: check which calendar or scheduling apps Zapier supports natively (Google Calendar, Calendly) before promising either.
3. ~~Conversion stage~~ (column and report line added 2026-11-24; still set by hand).
4. **WhatsApp and Facebook:** verify the native Zapier apps first (Facebook Lead Ads for the source; a WhatsApp or SMS app for the follow-up) and note any paid-plan or business-verification requirement. Until verified, the repo claims neither.
5. **Real recipients:** switch Zap 5 from the owner's address to the lead's address, only with the owner's explicit OK.

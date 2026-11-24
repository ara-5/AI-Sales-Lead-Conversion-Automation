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
| 6. Sales conversion | Lead is marked won or lost | - | **Not built.** No Converted/Won field or reporting of conversions yet |

Add-ons: Zap 7 lists leads with no contact after 3 days (owner only); Zap 8 emails a daily count summary (owner only). Neither has run on its schedule yet.

## What a live demo can honestly show today

1. Submit the demo form with a synthetic lead.
2. Show the row appear in `Leads`, then the AI's summary, budget, urgency, score, category and reason (a few minutes later, since Zapier polls).
3. Show the CRM row and `Approval Status = Pending Approval`.
4. Set `Approval Status` to `Approved` and show the one test-mode email and the `Sent At` stamp.
5. Type a `Booking Time` and show the owner notification.

Observed on separate synthetic leads in the test log (`tests/test-cases.md`): TC-01/TC-16/TC-17 (capture to CRM to pending approval), TC-18 (approved send), TC-19 (booking notification). One single lead has not yet been run through every stage in one sitting; that recorded run is the next thing to do.

## Gaps to close for a complete demo, in priority order

1. **Record one continuous run** of a single lead through stages 1-5 and save the screenshots and run-log evidence.
2. **Booking** that is created, not typed: check which calendar or scheduling apps Zapier supports natively (Google Calendar, Calendly) before promising either.
3. **Conversion stage:** add a Won/Lost column and show it in the Zap 8 report.
4. **WhatsApp and Facebook:** verify the native Zapier apps first (Facebook Lead Ads for the source; a WhatsApp or SMS app for the follow-up) and note any paid-plan or business-verification requirement. Until verified, the repo claims neither.
5. **Real recipients:** switch Zap 5 from the owner's address to the lead's address, only with the owner's explicit OK.

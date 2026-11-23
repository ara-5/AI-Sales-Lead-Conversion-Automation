# Zap 2: AI qualification and scoring

**Status:** Published in Zapier as "LeadFlow 02 - AI Qualification". Run end to end on 2026-11-23 with three synthetic leads (no budget, prompt injection, Arabic); see TC-07, TC-08 and TC-10 in `tests/test-cases.md`. TC-09 (invalid AI output) is not run.

**Paid plan:** the AI by Zapier step is marked Premium, so this Zap needs a paid plan after the free trial.

## Steps (5)
1. Google Sheets, New Spreadsheet Row (Team Drive, polling), worksheet Leads.
2. Filter: Status exactly matches `New`.
3. AI by Zapier (Standard model). The prompt follows `prompts/lead-qualification.md`, with the seven lead fields mapped inside the `<lead>` block. It returns 10 output fields.
4. Code by Zapier (JavaScript) validates the AI output. It fails safe to "Needs Human Review" on any error. Checks:
   - `lead_score` is an integer 0-100.
   - `lead_category` and `urgency` are in the allowed lists.
   - The category agrees with the score.
   - A non-empty `budget` must appear in Budget Raw or Raw Message.
   - The draft has no "discount" or "refund" and no `AED 123`-style amount unless the lead's own message has one. This check is conservative: a draft that repeats an amount only given in the Budget field is also flagged.
   - It returns `validation_status`, `final_category`, `final_message`, `final_status` and `failure_reason`. If the category is "Needs Human Review", the draft is dropped.
5. Google Sheets, Update Spreadsheet Row (Row ID from step 1). It writes AI Summary, Requirements, Budget AI, Urgency, Score, Category, Score Reason, Next Action, Draft Message, Validation Status and Status (`Qualified` or `Needs Human Review`). Other columns are untouched.

## What was actually tested
- Step test on row 2 (Test Buyer One): AI returned Hot, score 95, budget quoted as typed, valid JSON-style fields. Validation returned VALID and row 2 was updated with Status `Qualified`.
- Live runs: all three test leads were written back with Validation `VALID` and Status `Needs Human Review`. The AI sent each to human review, so a live Hot lead with a draft has only been seen in the step test (row 2), not in a live run.
- Not tested: invalid AI output (TC-09) and the validation failure branches, failure notifications.

## Known gaps
- No alert is sent when validation fails. The lead is only marked Needs Human Review.
- The failure-notification Zapier setting is not configured.
- Zapier warns of a possible loop because step 5 edits the same sheet. The trigger fires only on new rows, so an update should not retrigger it. This is to be confirmed in a live run.

# Zap 4: Approval request (sheet-based)

**Status:** Published in Zapier as "LeadFlow 04 - Approval Request". The step test was skipped on purpose; it was tested with live runs on 2026-11-24 (see TC-17).

**What it does:** the draft follow-up message already comes from Zap 2 (`Draft Message`). This Zap marks a qualified lead's draft as waiting for a human decision by setting `Approval Status = Pending Approval` in the `Leads` sheet. It sends nothing to the lead and sends no email or message to anyone.

## Steps (3)
1. Google Sheets, New or Updated Spreadsheet Row, worksheet `Leads`, trigger column `Status`.
2. Filter, all three must hold: Status exactly matches `Qualified`; Draft Message exists; Approval Status does not exist. (Copilot first set the last rule to "Exists"; it was corrected to "Does not exist" and read back.)
3. Google Sheets, Update Spreadsheet Row in `Leads` (Row = step 1 Row ID), setting only `Approval Status = Pending Approval`. Every other column is left empty.

## How a human approves (design for Zap 5)
A person reviews the row's `Draft Message` and sets `Approval Status` to `Approved` and fills `Approved By`. Zap 5 will send only when Approval Status is `Approved`. Nothing in Zap 4 sends.

## What was tested
- Filter and mapping were read back from the editor after correcting the third filter rule.
- Negative case: a synthetic lead named "Test Approval Flow" scored 90 but the AI routed it to `Needs Human Review` (it judged the name test-related). It got no `Approval Status` and no CRM row, so the filters blocked it as intended.
- Positive case: a second synthetic lead ("Omar Demo Buyer", AED 3.2 million townhouse) went through Zaps 1-4. Result in the sheet: Status `Qualified`, score 92, category Hot, `Approval Status = Pending Approval`, and one row in the `CRM` tab.
- Not tested: a re-run on an already-pending row (the loop warning), and any failure branch.

## Known gaps
- No notification tells the approver that a draft is waiting. Adding one means sending an email to the approver's own address, which needs the owner's OK.
- Zapier flags a possible loop because step 3 edits the same sheet as the trigger. The trigger column is Status and the filter requires Approval Status to be empty, so a re-run should not happen. One live run showed no extra change, but a deliberate re-run was not tested.

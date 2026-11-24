# Zap 4: Approval request (sheet-based)

**Status:** Built in Zapier as "LeadFlow 04 - Approval Request" (draft; not published and not run at the time of writing). The step test was skipped on purpose so the first run is a real lead.

**What it does:** the draft follow-up message already comes from Zap 2 (`Draft Message`). This Zap marks a qualified lead's draft as waiting for a human decision by setting `Approval Status = Pending Approval` in the `Leads` sheet. It sends nothing to the lead and sends no email or message to anyone.

## Steps (3)
1. Google Sheets, New or Updated Spreadsheet Row, worksheet `Leads`, trigger column `Status`.
2. Filter, all three must hold: Status exactly matches `Qualified`; Draft Message exists; Approval Status does not exist. (Copilot first set the last rule to "Exists"; it was corrected to "Does not exist" and read back.)
3. Google Sheets, Update Spreadsheet Row in `Leads` (Row = step 1 Row ID), setting only `Approval Status = Pending Approval`. Every other column is left empty.

## How a human approves (design for Zap 5)
A person reviews the row's `Draft Message` and sets `Approval Status` to `Approved` and fills `Approved By`. Zap 5 will send only when Approval Status is `Approved`. Nothing in Zap 4 sends.

## What was tested
- The filter conditions and the update mapping were read back from the editor after the fix. No step test or live run has happened yet.

## Known gaps
- No notification tells the approver that a draft is waiting. Adding one means sending an email to the approver's own address, which needs the owner's OK.
- Zapier flags a possible loop because step 3 edits the same sheet as the trigger. The trigger column is Status and the filter requires Approval Status to be empty, so a re-run should not happen; this is not yet confirmed in a live run.

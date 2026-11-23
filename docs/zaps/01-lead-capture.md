# Zap 1 — LeadFlow: New Lead Capture

**Purpose:** Take each new Google Form response, clean it, reject duplicates, and create one row in `Leads` with Status `New`.
**Status:** Designed, not yet built or tested. Step names below must be checked against the Zap editor.

## Prerequisite sheets
Spreadsheet `LeadFlow AI Demo`
- Tab `Form_Responses` (auto-created by the Google Form; link the form to this spreadsheet).
- Tab `Leads` with headers (row 1): `Lead ID | Created At | Source | Full Name | Email | Phone | Company | Interest | Budget Raw | Location | Raw Message | Dedupe Key | Status | AI Summary | Requirements | Budget AI | Urgency | Score | Category | Score Reason | Next Action | Draft Message | Validation Status | CRM ID | Approval Status | Approved By | Sent At | Booking Time | Last Contacted | Owner | Escalated`

## Form fields (all required except Company/Budget)
Full name, Email, Phone, Company, What are you looking for?, Budget, Preferred location, Message.

## Steps
1. **Google Sheets → New Spreadsheet Row** (trigger): `Form_Responses`.
2. **Filter**: Email (Exists) AND Email (Contains) `@`.
3. **Formatter → Text → Trim Whitespace** on Email.
4. **Formatter → Text → Lowercase** on the step 3 output.
5. **Formatter → Numbers → Format Phone Number** on Phone (check the available output formats; the goal is a consistent international format such as +971...).
6. **Formatter → Date/Time → Format** on the Zap run time (`zap_meta_human_now`) to `YYYYMMDDHHmmss`, used for the Lead ID.
7. **Google Sheets → Lookup Spreadsheet Row** in `Leads`, column `Dedupe Key` = normalized email. "Create row if none found" off.
8. **Filter**: step 7 Row ID (Does not exist).
9. **Google Sheets → Lookup Spreadsheet Row** in `Leads`, column `Phone` = normalized phone.
10. **Filter**: step 9 Row ID (Does not exist).
11. **Google Sheets → Create Spreadsheet Row** in `Leads`: Lead ID `LF-` + step 6 output, Created At = form Timestamp, Source `Google Form`, normalized Email/Phone, other fields mapped from the trigger, Dedupe Key = normalized email, Status `New`.

## Duplicate handling
MVP: duplicates halt at a filter (visible as "Filtered" in Zap history). Improvement: log duplicates to a `Duplicates_Log` tab (needs Paths or a second Zap; check plan availability).

## Failure handling
Enable Zapier failed-run notifications. Check Zap History for the failing step's input/output.

## Tests
See TC-01, TC-02, TC-05, TC-06, TC-07 in `tests/test-cases.md`.

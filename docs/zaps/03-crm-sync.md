# Zap 3: CRM create/update (Google Sheet CRM)

**Status:** Published in Zapier as "Sync Qualified Leads to CRM". Step-tested on 2026-11-23 with "Sara Demo Buyer", then run live end to end with a new synthetic lead ("Test CRM Sync") and an update re-run; see TC-16.

**Why a Google Sheet CRM:** the demo needs no external account. A HubSpot version would need a HubSpot account connected in Zapier. HubSpot's free CRM tier exists, but compatibility with the user's Zapier plan was not verified.

## Steps (4)
1. Google Sheets, New or Updated Spreadsheet Row, spreadsheet "LeadFlow AI Demo", worksheet `Leads`, trigger column `Status`.
2. Filter: Status exactly matches `Qualified`. Leads marked Needs Human Review are not copied to the CRM.
3. Google Sheets, Lookup Spreadsheet Row in worksheet `CRM` by `Dedupe Key`, with "create row if it doesn't exist" on. A new lead gets a CRM row holding only its Dedupe Key; an existing lead returns its row.
4. Google Sheets, Update Spreadsheet Row in `CRM` (Row = step 3 ID). Writes Dedupe Key, Lead ID, Full Name, Email, Phone, Company, Source, Lead Score, Lead Category, Urgency, Budget, Lead Summary, Next Action, Lead Status and `CRM Action = Synced`.

The `CRM` tab has 17 columns: Dedupe Key, Lead ID, Full Name, Email, Phone, Company, Source, Lead Score, Lead Category, Urgency, Budget, Lead Summary, Next Action, Lead Status, First Created, Last Updated, CRM Action.

## What was actually tested
- Step tests: the lookup created a CRM row holding only the Dedupe Key (row ID 2 at the time), and the update step then filled it with all mapped fields and `CRM Action = Synced`.
- Live chain: a new synthetic enquiry ("Test CRM Sync", Dubai Marina apartment, AED 2.1 million) went through Zap 1, Zap 2 (score 94, Hot, `Qualified`) and Zap 3, which added a second row to the `CRM` tab with `CRM Action = Synced`.
- Update path: the lead's Company in `Leads` was edited, then Status was flipped to `Needs Human Review` and back to `Qualified` so the trigger saw a change. The `CRM` tab still had 2 rows, and the test lead's Company read "Demo Crm Holdings Updated". One lead produced one CRM row.
- Not tested: any failure branch and failure alerts.

## Known gaps
- First Created and Last Updated are not populated yet.
- The trigger column is Status, so an edit to another column (for example Company) does not by itself sync. The update above only appeared after Status changed.
- No alert on failure beyond Zapier's default error email.
- The Lead ID format (slashes and colons) is unchanged from Zap 1.

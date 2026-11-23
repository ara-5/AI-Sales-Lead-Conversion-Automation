# Zap 3: CRM create/update (Google Sheet CRM)

**Status:** Built in Zapier as "Sync Qualified Leads to CRM" (draft, not published at the time of writing). Steps 3 and 4 were run as step tests on 2026-11-23 with the synthetic lead "Sara Demo Buyer". It has not yet run live from the trigger.

**Why a Google Sheet CRM:** the demo needs no external account. A HubSpot version would need a HubSpot account connected in Zapier. HubSpot's free CRM tier exists, but compatibility with the user's Zapier plan was not verified.

## Steps (4)
1. Google Sheets, New or Updated Spreadsheet Row, spreadsheet "LeadFlow AI Demo", worksheet `Leads`, trigger column `Status`.
2. Filter: Status exactly matches `Qualified`. Leads marked Needs Human Review are not copied to the CRM.
3. Google Sheets, Lookup Spreadsheet Row in worksheet `CRM` by `Dedupe Key`, with "create row if it doesn't exist" on. A new lead gets a CRM row holding only its Dedupe Key; an existing lead returns its row.
4. Google Sheets, Update Spreadsheet Row in `CRM` (Row = step 3 ID). Writes Dedupe Key, Lead ID, Full Name, Email, Phone, Company, Source, Lead Score, Lead Category, Urgency, Budget, Lead Summary, Next Action, Lead Status and `CRM Action = Synced`.

The `CRM` tab has 17 columns: Dedupe Key, Lead ID, Full Name, Email, Phone, Company, Source, Lead Score, Lead Category, Urgency, Budget, Lead Summary, Next Action, Lead Status, First Created, Last Updated, CRM Action.

## What was actually tested
- Filter step: configured as above; its test passed on the sample row (Status `Qualified`).
- Step 3 (create path): the first retest of the lookup created a CRM row with only the Dedupe Key for `sara.demo.buyer@example.com` and returned row ID 2.
- Step 4: after the Row mapping was fixed, the test filled that row with all mapped fields and `CRM Action = Synced`. Confirmed by reading the CRM tab back.
- Not tested: the update path for a lead that already has a CRM row (duplicate prevention), a live run triggered by Zap 2 changing Status, and any failure branch.

## Known gaps
- First Created and Last Updated are not populated yet.
- Zap 3 triggers on any update to a `Leads` row, then the filter drops non-Qualified rows. Later edits to a Qualified row will re-run the update, which is harmless but uses tasks.
- No alert on failure beyond Zapier's default error email.
- The Lead ID format (slashes and colons) is unchanged from Zap 1.

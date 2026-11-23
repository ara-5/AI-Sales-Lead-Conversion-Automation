# Architecture

```mermaid
flowchart TD
  A[Google Form] --> S1[(Sheet: Form_Responses)]
  W[Website form via webhook - later] --> Z1
  S1 --> Z1[Zap 1: Lead capture, normalize, dedupe]
  Z1 --> L[(Sheet: Leads, Status=New)]
  L --> Z2[Zap 2: AI qualification + validation]
  Z2 -->|valid| L2[Leads: score, category, reason, draft]
  Z2 -->|invalid/incomplete| H[Category = Needs Human Review + alert]
  L2 --> Z3[Zap 3: CRM create/update]
  L2 --> Z4[Zap 4: Follow-up draft + approval request]
  Z4 --> AP{Human approves?}
  AP -->|yes| Z5[Zap 5: Gmail send]
  AP -->|no| H
  CAL[Calendly / Google Calendar] --> Z6[Zap 6: Booking notification]
  SCH1[Schedule] --> Z7[Zap 7: Inactive lead reminders + escalation]
  SCH2[Schedule] --> Z8[Zap 8: Daily/weekly report]
  Z3 & Z5 & Z6 & Z7 --> N[Slack or email notifications]
```

## Data model: sheet `Leads` (single source of truth for the MVP)
| Column | Set by |
|---|---|
| Lead ID, Created At, Source | Zap 1 |
| Full Name, Email (normalized), Phone (normalized), Company | Zap 1 |
| Interest, Budget (raw text), Location, Raw Message | Zap 1 |
| Dedupe Key, Status | Zap 1 (`New`) |
| AI Summary, Requirements, Budget (AI), Urgency, Score, Category, Score Reason, Next Action, Draft Message, Validation Status | Zap 2 |
| CRM ID | Zap 3 |
| Approval Status, Approved By, Sent At | Zap 4/5 |
| Booking Time | Zap 6 |
| Last Contacted, Owner, Escalated | Zap 5/7 |

## Design rules
- Raw customer text is treated as data, never as instructions to the AI.
- The AI may only extract what the lead stated; unknown values are `null`.
- Nothing is emailed to a lead without a recorded human approval.
- Integrations (HubSpot, Calendly, Slack) are used only after verified in the Zap editor; fallback is Google Sheets/Gmail/Google Calendar.

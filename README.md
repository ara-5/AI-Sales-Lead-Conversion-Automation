# LeadFlow AI

**AI lead capture, qualification and follow-up automation for UAE real estate agencies, clinics, training institutes and SMEs. Built on Zapier with native integrations and human approval before anything sensitive is sent.**

![Status](https://img.shields.io/badge/status-in%20progress-orange)
![Zaps live](https://img.shields.io/badge/Zaps%20live-8%20built-blue)
![Engine](https://img.shields.io/badge/engine-Zapier-ff4a00)
![Data](https://img.shields.io/badge/data-synthetic%20only-green)

> **One experience, not eight workflows.** New lead → AI qualification (intent, budget, urgency, requirements) → score and CRM update → personalised follow-up with human approval → appointment booked → sales conversion. The Zaps below are stages of that single journey; one lead has been run through capture, qualification, CRM, approval, send and booking in about 14 minutes (see [`docs/end-to-end-demo.md`](docs/end-to-end-demo.md), TC-21), and that page lists exactly which stages work today and which do not (notably: only the website form is a lead source, follow-up is email in test mode, booking time is entered by hand, and there is no WhatsApp, Facebook or conversion tracking yet).

> **Project status: IN PROGRESS.** Only the rows marked live below have been built and tested. Everything else is design. All data in this repo is synthetic.

## The problem
Slow replies, forgotten follow-ups, and leads scattered across channels cost revenue. LeadFlow AI captures every enquiry, cleans and de-duplicates it, scores it with a stated reason, drafts a personalised follow-up, and keeps a human in the loop before anything is emailed to a customer.

## How it works

```mermaid
flowchart LR
  F[Google Form] --> Z1["Zap 1<br/>Capture, normalise, dedupe"]
  Z1 --> L[("Leads sheet<br/>Status: New")]
  L --> Z2["Zap 2<br/>AI score + validation"]
  Z2 -->|valid + Hot/Warm/Cold| Q["Status: Qualified"]
  Z2 -->|invalid or unsure| H["Status: Needs Human Review"]
  Q --> Z3["Zap 3<br/>CRM create / update"]
  Z3 --> C[("CRM sheet<br/>one row per lead")]
  Q --> Z4["Zap 4<br/>Mark Pending Approval"]
  Z4 --> A{Human approves?}
  A -->|yes| G["Zap 5<br/>Gmail send (test mode)"]
  C --> Z6["Zap 6<br/>Booking notification (test mode)"]
  Q --> Z7["Zap 7 Inactive digest<br/>9:00 AM, owner only"]
  Q --> Z8["Zap 8 Daily report<br/>6:00 PM, owner only"]
  classDef live fill:#e6f4ea,stroke:#1e8e3e,color:#000;
  classDef planned fill:#f1f3f4,stroke:#9aa0a6,color:#555,stroke-dasharray: 4 3;
  class F,Z1,L,Z2,Q,H,Z3,C,Z4,A,G,Z6,Z7,Z8 live;
```

Solid green is built and published (test mode).

## Build status (stages of the one journey; Zaps 7-8 are optional add-ons)
| # | Zap | Status |
|---|---|---|
| 1 | Lead capture, normalise, dedupe (Google Form → Sheet) | **Live (v2).** Tested with a valid lead, no budget, invalid email, duplicate email and duplicate phone |
| 2 | AI qualification, scoring and validation | **Live.** Run end to end with hot, no-budget, prompt-injection and Arabic leads. Invalid-output test not run |
| 3 | CRM create/update (Google Sheet CRM) | **Live.** One synthetic lead run through Zaps 1 → 2 → 3, then re-run to confirm one CRM row per lead |
| 4 | Approval request (marks drafts Pending Approval; sends nothing) | **Live.** Qualified lead became Pending Approval; a Needs Human Review lead was blocked |
| 5 | Approved send (test mode, emails owner only) | **Live (v3, test mode).** One approved lead produced one send and a `Sent At` stamp; a pending lead was blocked |
| 6 | Booking notification (test mode, emails owner only) | **Live (v1, test mode).** A Booking Time on a lead produced one owner-only notification |
| 7 | Inactive lead daily digest (test mode, emails owner only) | **Live (test mode).** Steps tested individually, one owner-only test digest sent; first scheduled run (2026-11-25 09:00) fired and was Filtered, nothing sent |
| 8 | Daily sales report (test mode, emails owner only) | **Live (test mode).** Report counts verified against the sheet; one owner-only test email sent; first scheduled 6 PM run not yet observed |
| 9 | Failure alert (test mode, emails owner only) | **Live (v1).** Sample alert sent; no real Zap error has triggered it yet |

Details per Zap are in [`docs/zaps/`](docs/zaps/README.md). Test cases and honest results are in [`tests/test-cases.md`](tests/test-cases.md).

## Safeguards built in
- **Customer text is data, never instructions.** The AI prompt wraps the lead in a data block, and a prompt-injection lead was tested (TC-08).
- **The AI may only extract what the lead said.** A Code step checks that any budget it returns appears in the lead's own text, so an invented figure is rejected.
- **Validation before anything is stored as Qualified.** A Code step checks the score range, the category list, score/category agreement and banned phrases in the draft, and fails safe to Needs Human Review.
- **Duplicate prevention.** Email is lowercased and phone is normalised to a single UAE format. Both are looked up before a row is created. A duplicate-phone bug found in testing was fixed in v2 (TC-06).
- **One CRM row per lead.** Zap 3 looks the lead up by Dedupe Key before it writes (TC-16).
- **Human approval for sensitive sends** is part of the design for Zaps 4-5. Nothing is emailed to a lead yet.

## Tested so far
| Test | What it shows | Result |
|---|---|---|
| TC-01 | Hot buyer is scored Hot with a reason; the draft promises no price or discount | Pass |
| TC-03, TC-05 | Blank email and same email in a different case never create a row | Pass |
| TC-06 | Same phone in a different format is caught (failed on v1, fixed in v2) | Pass after fix |
| TC-07 | No budget given: no budget is invented | Pass |
| TC-08 | Prompt injection is not obeyed; routed to Needs Human Review | Pass (one run) |
| TC-10 | Arabic message handled; budget quoted as written | Pass under the "or Needs Human Review" clause |
| TC-04, TC-12 | Spam and a refund/legal-threat lead are routed to Needs Human Review with no draft | Pass |
| TC-02, TC-11 | Vague renter and clinic booking with an insurance question: both were sent to Needs Human Review, not Warm/Cold or Qualified as expected | **Did not match expectation** (conservative; prompt not tuned) |
| TC-16 | Qualified lead creates one CRM row, and a re-run updates it | Pass |
| TC-17 | Qualified lead is marked Pending Approval; a Needs Human Review lead is not | Pass |
| TC-09 | Invalid AI output triggers failure handling and an alert | **Not run** |

## Known limitations
- No alert is sent when validation fails. Zapier's default error email is the only notification, and custom notifications need a paid plan.
- The Lead ID format has slashes and colons and could collide.
- The CRM `First Created` and `Last Updated` columns are not populated.
- The AI by Zapier step is a Premium feature, so Zap 2 needs a paid plan after the free trial.

## Screenshots
Captured 2026-11-24 and cropped so that only LeadFlow Zaps and synthetic data are visible (account and folder names are cropped out, not blurred).

**All eight Zaps switched on**

![Zapier Zaps list showing LeadFlow 01-08 switched on](screenshots/zaps-live.png)

**Leads sheet with synthetic test leads** (test emails use `example.com`)

![Leads sheet with synthetic leads](screenshots/leads-sheet.png)

**Zap 8 - Daily sales report (Schedule, Sheets, Python, Gmail)**

<img src="screenshots/zap8-daily-report.png" alt="Zap 8 canvas with four steps" width="320">

Not captured: the CRM tab, the other seven Zap canvases, and a received report email.

## Target customers
UAE real estate agencies, property consultants, clinics, training institutes, service companies and other SMEs. Currency, phone format, language and category lists are configuration, not code, so it adapts to international clients.

## Repository layout
```
README.md
docs/architecture.md           architecture diagram and data model
docs/zaps/README.md            status of every Zap
docs/zaps/01-lead-capture.md
docs/zaps/02-ai-qualification.md
docs/zaps/03-crm-sync.md
docs/zaps/04-approval-request.md
docs/zaps/05-approved-send.md
docs/zaps/06-booking-notification.md
docs/zaps/07-inactive-lead-digest.md
docs/zaps/08-daily-sales-report.md
docs/commercial-deployment.md  pricing and rollout notes
prompts/lead-qualification.md  AI prompt, JSON schema, validation rules
data/synthetic_leads.csv       synthetic test leads
tests/test-cases.md            test cases and results
screenshots/                   Zap list, Leads sheet and Zap 8 canvas
```

## Sharing Zaps safely
Zaps are not source code, so they are documented as written specs plus screenshots. No API keys, connection names, real emails, webhook URLs or sheet IDs are committed. Account names are blurred in screenshots.

## Roadmap
Zaps 4-8, WhatsApp lead source, multi-language drafts, Calendly vs Google Calendar option, per-client config sheet, SaaS migration path.

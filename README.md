# LeadFlow AI — Intelligent Sales & Lead Conversion Automation (Zapier)

LeadFlow AI captures inbound enquiries, normalizes and de-duplicates them, qualifies and scores them with AI (with a stated reason), drafts personalized follow-ups, and routes everything through human approval before anything sensitive is sent. Zapier is the orchestration engine.

## Build status
| Zap | Status |
|---|---|
| 1. Lead capture, normalize, dedupe (Google Form → Sheet) | **Live (v2).** Tested with synthetic leads: valid lead, no budget, invalid email, duplicate email, duplicate phone |
| 2. AI qualification and validation | **Live.** Run end to end with synthetic no-budget, prompt-injection and Arabic leads (all routed to Needs Human Review). Invalid-output test not run |
| 3. CRM create/update (Google Sheet CRM) | **Built, not yet published.** Create path and field mapping step-tested with one synthetic lead; update path for an existing lead and a live run not yet tested |
| 4-8. Follow-up, approval, booking, reminders, report | Not started |

Zap 1 uses Zapier features that need a paid plan after the free trial.

> **Project status: IN PROGRESS.** Nothing below is claimed as working until the matching row in [`docs/zaps/README.md`](docs/zaps/README.md) and [`tests/test-cases.md`](tests/test-cases.md) says it was tested. All data in this repo is synthetic.

## Target customers
UAE real estate agencies, property consultants, clinics, training institutes, service companies and other SMEs. Adaptable for international clients (currency, phone format, language and category lists are configuration, not code).

## Problem
Slow replies, forgotten follow-ups, and leads scattered across channels cost revenue.

## Features (MVP target)
Lead capture · normalization · duplicate detection · AI qualification and scoring with reasons · Hot/Warm/Cold/Needs Human Review · AI follow-up drafts · human approval · Gmail send · CRM record (HubSpot if available, otherwise a Google Sheet) · booking notifications · rep notifications · inactive-lead reminders and escalation · daily/weekly reports.

## Repository layout
```
README.md
docs/architecture.md        architecture diagram + data model
docs/zaps/README.md         status of every Zap
docs/zaps/01-lead-capture.md
docs/commercial-deployment.md
prompts/lead-qualification.md  AI prompt, JSON schema, validation rules
data/synthetic_leads.csv    synthetic test leads
tests/test-cases.md         test cases and results (updated as we test)
screenshots/                add real screenshots after each Zap is tested
```

## Sharing Zaps safely
Zaps are not source code. Document them as written specs plus screenshots. Never commit API keys, connection names, real emails, webhook URLs, or sheet IDs. Blur account names in screenshots. Use Zapier's Zap sharing/copy links only for template Zaps that contain no customer data.

## Roadmap
WhatsApp lead source, multi-language drafts, Calendly vs Google Calendar option, per-client config sheet, SaaS migration path.

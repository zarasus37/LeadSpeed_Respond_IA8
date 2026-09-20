# LeadSpeed Respond — SKU #1 Template Pack (IA8)

**Product:** Missed-call + web-form SMS text-back  
**Stack (locked):** Make.com + Twilio SMS + Google Sheets (+ Google Forms)  
**Audience:** Second (and later) clients — start from templates, not blank  
**Version:** IA8 delivery pack

---

## What this is

A complete, secrets-free delivery kit so an implementer can stand up LeadSpeed Respond for a new client in a bounded setup window (see `15_Setup_Timebox.md`). Message copy is **not** final — the Writer fills `Message_Slots` using placeholder keys.

## Stack

| Layer | Tool | Role |
|-------|------|------|
| Orchestration | Make.com | 5 scenarios + shared modules |
| Voice / SMS | Twilio | Missed-call Dial + SMS send/receive |
| Data | Google Sheets | Config, Lead_Log, Follow_ups, Message_Slots, QA_Log |
| Intake (web) | Google Forms (or webhook) | Web-form leads |
| Optional | Jobber / Housecall Pro | Soft-fail adapter only — never blocks core |

**Out of scope:** QuoteChase, ReviewBoost, deep CRM, IVR menus, AI chatbots, ads.

## Order of setup

1. Read `00_DELIVERY_MAP.md` (architecture + acceptance).
2. Create Google Sheet from `01_Sheet_Template/` + validate against `01_schema.json`.
3. Fill Config keys per `13_Config_Keys.md` (placeholders only until client secrets go in Make connections).
4. Provision Twilio (number, TwiML App / voice webhook) using `07_TwiML_voice_forward.xml`.
5. Build Make scenarios 1→5 from blueprints `02`–`06`; implement shared modules from `shared_modules.md` first.
6. Writer replaces placeholders in `09_Message_Slots.csv` → Sheet `Message_Slots` tab.
7. Run every gate in `10_QA_Checklist.md`; log results in `QA_Log`.
8. Handoff with `11_Client_Runbook.md`; schedule retainer via `12_Retainer_Checklist.md`.

Optional paths: `08_OpenPhone_alternate.md`, `14_Optional_Jobber_HCP_Adapter.md`.

## No-secrets rule

- **Never** commit or paste API keys, Auth Tokens, Account SIDs, or real phone numbers into this pack or client-facing docs.
- Use placeholders: `{{TWILIO_ACCOUNT_SID}}`, `{{TWILIO_AUTH_TOKEN}}`, `{{MAKE_WEBHOOK_A_URL}}`, `+1XXXXXXXXXX`.
- Live credentials live only in Make.com connections / Twilio console / client vault.

## Handoff to Writer

After Sheet + Config exist, give Writer:

- `09_Message_Slots.csv` (slot keys + placeholder bodies + merge vars)
- Sheet tab `Message_Slots`
- Product facts: Instant 0s · Touch1 +30m · Touch2 +24h · Touch3 +72h · quiet hours 08:00–20:00 America/Chicago · STOP/HELP behavior

Writer output replaces every `[WRITER: …]` body. Implementer does not invent final SMS copy.

## Pricing context (docs only)

| Item | Amount |
|------|--------|
| Setup | $1,297 (6–10h) |
| Retainer | $497/mo (2–3h/mo) |

## Latency promise

Median <30s from unanswered call / form submit to first SMS; hard ceiling <60s.

## File index

| # | File | Purpose |
|---|------|---------|
| — | `README.md` | This file |
| — | `shared_modules.md` | NormalizePhone, IsPaused, Dedup, ScheduleTouches, ErrorHandler |
| 00 | `00_DELIVERY_MAP.md` | Architecture, scenarios, acceptance |
| 01 | `01_Sheet_Template/` + `01_schema.json` | Sheet tabs + schema |
| 02–06 | `02`…`06_Make_Scenario*.json` | Make blueprint specs |
| 07 | `07_TwiML_voice_forward.xml` | Dial timeout=20 |
| 08 | `08_OpenPhone_alternate.md` | Non-Twilio dial path |
| 09 | `09_Message_Slots.csv` | Writer placeholders |
| 10 | `10_QA_Checklist.md` | QA gates |
| 11 | `11_Client_Runbook.md` | Client ops |
| 12 | `12_Retainer_Checklist.md` | Monthly retainer |
| 13 | `13_Config_Keys.md` | Config reference |
| 14 | `14_Optional_Jobber_HCP_Adapter.md` | Soft-fail CRM |
| 15 | `15_Setup_Timebox.md` | Hours by phase |

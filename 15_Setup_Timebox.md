# 15 — Setup Timebox

Target total **6–10 hours** setup (matches pricing context $1,297). Stay inside phase caps; escalate scope creep separately.

| Phase | Hours | Activities |
|-------|-------|------------|
| Twilio | **1–2h** | Number ready; Voice webhook A; Dial action B; Messaging → Scenario 5; test Dial timeout=20; geo/SMS permissions |
| Google Sheet | **0.5h** | Create spreadsheet from `01_Sheet_Template/`; verify columns vs `01_schema.json`; fill Config (client values); paste Writer Message_Slots when ready |
| Make.com | **3–4h** | Shared modules first (`shared_modules.md`); Scenarios 1→5 from blueprints `02`–`06`; connections Twilio+Sheets; optional Jobber/HCP soft-fail last |
| QA | **1–1.5h** | Full `10_QA_Checklist.md` A1–A12 (skip A12 if adapters off); log QA_Log |
| Handoff | **0.5–1h** | Walk `11_Client_Runbook.md`; pause/Won/Lost; escalation path; schedule retainer cadence (`12_Retainer_Checklist.md`) |

## Order (do not reorder casually)

1. Sheet + Config skeleton  
2. Twilio webhooks + TwiML (`07`)  
3. Shared Make modules  
4. Scenario 1 (Missed Call) + A1/A2  
5. Scenario 5 (Inbound SMS) + A6–A8  
6. Scenario 4 (Dispatcher) + A5  
7. Scenario 2 (Web Form) + A3/A4  
8. Scenario 3 (Manual Test) + A10  
9. Writer slots → retest Instant copy  
10. Optional adapter (`14`)  
11. Client handoff  

## Buffer rules

- If Make >4h: stop inventing features; use blueprint as-is; defer OpenPhone (`08`) and CRM adapters.
- If Twilio voice blocked (client on OpenPhone): switch to `08_OpenPhone_alternate.md`; still count under Twilio/voice phase.
- Secrets only in Make/Twilio consoles — never in Sheet cells named “api_key”.

# 00 — Delivery Map (IA8 from IA7)

Condensed, buildable map for LeadSpeed Respond SKU #1.

---

## 1. Architecture

```
                    ┌─────────────────┐
   Inbound call ───►│ Twilio Voice    │── Dial timeout=20s ──► Owner/tech phone
                    │ Webhook A TwiML │         │
                    └─────────────────┘         │ no-answer/busy/failed/canceled
                                               ▼
                                        Webhook B (Make)
                                               │
   Google Form / webhook ──────────────────────┤
   Manual test webhook ────────────────────────┤
                                               ▼
                                    ┌──────────────────────┐
                                    │ Make.com Scenarios   │
                                    │ + Shared Modules     │
                                    └──────────┬───────────┘
                                               │
                    ┌──────────────────────────┼──────────────────────────┐
                    ▼                          ▼                          ▼
             Google Sheets                 Twilio SMS              Optional Jobber/HCP
             Config / Lead_Log             Instant + touches       (soft-fail only)
             Follow_ups / Message_Slots    Inbound STOP/HELP
             QA_Log
```

**Latency:** Instant SMS median <30s, hard <60s after unanswered determination or form submit.

**Touches:** Instant @ 0s → Touch1 +30m → Touch2 +24h → Touch3 +72h → stop. Quiet hours 08:00–20:00 America/Chicago (roll forward).

---

## 2. Scenarios (Make)

| # | Name | Trigger | Core behavior |
|---|------|---------|---------------|
| 1 | Missed Call | Webhook A (TwiML) + Webhook B (status) | A returns Dial 20s; B intakes only DialCallStatus in {no-answer, busy, failed, canceled}; never on completed |
| 2 | Web Form | Forms watch / custom webhook | Normalize phone; invalid → Error + owner alert, **no** lead SMS |
| 3 | Manual Test | Secret webhook URL | Same pipeline as form; `source=manual_test` |
| 4 | Follow-up Dispatcher | Schedule every 10 min | Pending where send_at ≤ now; skip OptOut/Replied/Won/Lost/paused |
| 5 | Inbound SMS | Twilio SMS webhook | STOP→OptOut; HELP→help_reply; else Replied + cancel pending + owner alert |

Shared: NormalizePhone, IsPaused, Dedup (120m), ScheduleTouches, ErrorHandler — see `shared_modules.md`.

---

## 3. Data model (Sheet tabs)

| Tab | Role |
|-----|------|
| Config | Key/value runtime settings |
| Lead_Log | One row per lead attempt |
| Follow_ups | Touches 1–3 schedule + status |
| Message_Slots | Writer-owned SMS bodies |
| QA_Log | Test evidence |

Full columns: `01_schema.json` + `01_Sheet_Template/` + `13_Config_Keys.md`.

---

## 4. Missed-call status gate (critical)

Webhook B processes **only** when Twilio `DialCallStatus` (or equivalent) is:

- `no-answer`
- `busy`
- `failed`
- `canceled`

**Never** send Instant SMS when status is `completed` (human answered).

---

## 5. Acceptance criteria

| ID | Gate |
|----|------|
| A1 | Unanswered call → Instant SMS <60s; Lead_Log row; 3 Pending Follow_ups |
| A2 | Answered call → **zero** Instant SMS; no new lead (or lead not Texted) |
| A3 | Web form valid phone → Instant + Follow_ups; invalid → Error + owner alert, no customer SMS |
| A4 | Dedup: second miss within 120m → no second Instant |
| A5 | Touch1/2/3 fire near scheduled times; respect quiet hours |
| A6 | Inbound reply → status Replied; Pending Follow_ups Canceled; owner alerted |
| A7 | STOP → OptOut; confirm SMS; cancel Pending; no further touches |
| A8 | HELP → help_reply SMS; lead not OptOut |
| A9 | system_paused=TRUE → no outbound Instant/touches |
| A10 | Manual test webhook creates source=manual_test end-to-end |
| A11 | No secrets in Sheet formulas, Make blueprint exports shared with client, or this pack |
| A12 | Jobber/HCP failure does not block SMS (if adapter enabled) |

Map A1–A12 → `10_QA_Checklist.md` scenarios.

---

## 6. Scope

**IN:** Missed-call text-back, web-form text-back, 3 follow-ups, quiet hours, STOP/HELP, owner/tech alerts, Sheet ops, QA, retainer checklist.

**OUT:** QuoteChase, ReviewBoost, deep CRM sync, IVR trees, AI chatbots, ad platforms. Jobber/HCP = optional soft-fail only (`14_Optional_Jobber_HCP_Adapter.md`).

---

## 7. Setup budget

See `15_Setup_Timebox.md`: Twilio 1–2h · Sheet 0.5h · Make 3–4h · QA 1–1.5h · handoff 0.5–1h. Total setup target 6–10h.

---

## 8. Pricing (documentation only)

Setup $1,297 · Retainer $497/mo (2–3h/mo). Not encoded in automation logic.

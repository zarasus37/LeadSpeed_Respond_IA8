# Scenario 1 — Missed Call (companion notes)

JSON blueprint: `02_Make_Scenario1_MissedCall.json`

## Twilio console wiring

1. Voice number → **A CALL COMES IN** webhook: `POST {{MAKE_WEBHOOK_A_URL}}`
2. Webhook A responds with TwiML Dial `timeout="20"` `action="{{MAKE_WEBHOOK_B_URL}}"`
3. After dial ends, Twilio POSTs to Webhook B with `DialCallStatus`

## Critical filter

Process Instant SMS **only** if `DialCallStatus` ∈ {`no-answer`,`busy`,`failed`,`canceled`}.  
If `completed` → do nothing (human answered).

## Rebuild order in Make

1. Create Webhook A + Webhook Response (XML).
2. Create Webhook B as separate scenario path or second scenario linked by URL in TwiML.
3. Attach shared modules: NormalizePhone → IsPaused → Config load → Dedup → Lead_Log → Instant → alerts → ScheduleTouches.
4. Set error handler on Twilio Send SMS → ErrorHandler.

## Timing

Webhook B fires after up to ~20s dial timeout + network. Instant SMS should still land within <60s hard SLA from unanswered determination (usually well under 30s median).

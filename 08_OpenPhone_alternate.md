# 08 — OpenPhone Alternate (non-Twilio dial-forward)

Use when the client already rings on **OpenPhone** and does not want Twilio voice forwarding. SMS can still be Twilio (or OpenPhone SMS if Make has a connector — prefer Twilio SMS for consistency with this pack).

## Goal

Detect a **missed / unanswered** inbound call and fire the same Instant + Follow_ups pipeline as Scenario 1 Webhook B — without TwiML Dial.

## Pattern A — OpenPhone → Zapier/Make missed-call trigger

1. Enable OpenPhone automation / webhook for **missed call** (or “call ended, unanswered”).
2. Point to a Make custom webhook (reuse Scenario 1 path B intake — not TwiML A).
3. Map payload:
   - `caller_phone` ← OpenPhone `from` / contact number
   - `caller_name` ← contact name if present
   - `source` ← force `missed_call`
4. Run shared modules: NormalizePhone → IsPaused → Dedup → Lead_Log → Instant SMS (Twilio) → alerts → ScheduleTouches.

**Do not** send Instant if OpenPhone indicates the call was answered/completed.

## Pattern B — Parallel ring + Twilio only for SMS

1. Keep OpenPhone as the human ring path (no Twilio Dial).
2. Use OpenPhone “missed call” event only as the trigger (Pattern A).
3. All outbound SMS still via Twilio `twilio_from_number` in Config.

## Gaps vs native Twilio Dial

| Capability | Twilio Dial pack | OpenPhone alternate |
|------------|------------------|---------------------|
| Dial timeout 20s precise | Yes (`07_TwiML…`) | Depends on OpenPhone missed definition |
| DialCallStatus enum | Exact | Map vendor statuses carefully |
| Single number stack | Voice+SMS Twilio | Voice OpenPhone + SMS Twilio |

## Implementation notes

- Document the OpenPhone status values that mean “unanswered” in the client runbook before go-live.
- QA gate A1/A2 still apply: unanswered → SMS; answered → no SMS.
- Webhook A / TwiML file unused in this mode; keep files in pack for future Twilio voice clients.
- No secrets in docs — OpenPhone API keys only in Make connections.

## When to prefer Twilio voice

New number, porting into Twilio, or client wants one vendor for voice+SMS and exact Dial timeout semantics.

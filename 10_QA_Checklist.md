# 10 — QA Checklist (IA7 §7 → IA8)

Log every run in Sheet tab **QA_Log** (`test_id`, `ran_at`, `scenario`, `pass_fail`, `notes`).  
Use Manual Test webhook + real unanswered/answered calls on a staging number. No production secrets in notes.

**Latency:** Instant median <30s · hard <60s from unanswered determination / form submit.

---

## Pre-flight

| ID | Check | Pass criteria |
|----|-------|---------------|
| P1 | Config filled | All keys in `13_Config_Keys.md` present; phones E.164 placeholders replaced with client test numbers in Sheet only |
| P2 | Message_Slots | No remaining `[WRITER:` placeholders in bodies used by live scenarios |
| P3 | Make connections | Twilio + Google Sheets connected; scenarios ON |
| P4 | Twilio webhooks | Voice → Webhook A; Dial action → B; Messaging → Scenario 5 |
| P5 | system_paused | FALSE before positive tests; TRUE for A9 |

---

## Gates A1–A12

### A1 — Unanswered Instant
- **Steps:** Call Twilio number; do not answer for >20s.
- **Pass:** Instant SMS to caller <60s after dial ends; Lead_Log `source=missed_call`, `status=Texted`, `instant_sms_sid` set; 3 Follow_ups `Pending` with send_at ≈ +30m/+24h/+72h (quiet-hours rolled); owner alert received; tech alert if `alert_tech=TRUE`.

### A2 — Answered = no SMS
- **Steps:** Call Twilio number; answer and hang up.
- **Pass:** `DialCallStatus=completed` path; **no** Instant SMS; no new Texted lead for that call.

### A3 — Web form valid / invalid
- **Valid:** Submit form with good US phone → Instant + Follow_ups + Lead_Log `web_form`.
- **Invalid:** Bad phone → Lead_Log `Error`, owner alert, **no** SMS to submitted number.

### A4 — Dedup 120m
- **Steps:** Trigger second missed call or form from same phone within 120 minutes.
- **Pass:** No second Instant SMS; dedup exit logged or silent per design.

### A5 — Follow-up Dispatcher
- **Steps:** Create/adjust a Pending Follow_up with `send_at` ≤ now (or wait for Touch1); run scheduler.
- **Pass:** SMS sent; Follow_ups `Sent` + `sms_sid`; Lead_Log `last_touch` updated; `status=FollowUp`. Quiet hours: if scheduled at night, send_at rolled to next 08:00 America/Chicago — no night send.

### A6 — Inbound reply
- **Steps:** From lead phone, text a normal reply (not STOP/HELP).
- **Pass:** Lead_Log `Replied`; all Pending Follow_ups `Canceled` `skip_reason=replied`; owner alert with snippet.

### A7 — STOP → OptOut
- **Steps:** Text `STOP`.
- **Pass:** `OptOut` + `opt_out=TRUE`; Pending canceled `opt_out`; `opt_out_confirm` SMS; no further touches on later dispatcher runs.

### A8 — HELP
- **Steps:** Text `HELP`.
- **Pass:** `help_reply` SMS; status **not** forced to OptOut; Follow_ups remain unless other rules apply.

### A9 — system_paused
- **Steps:** Set Config `system_paused=TRUE`; trigger missed call / form / due follow-up.
- **Pass:** No outbound Instant or touch SMS. Reset to FALSE after test.

### A10 — Manual Test
- **Steps:** POST sample body to secret webhook.
- **Pass:** Lead_Log `source=manual_test`; Instant + Follow_ups like form path.

### A11 — No secrets leakage
- **Steps:** Review Sheet, Make blueprint exports shared with client, this pack copy.
- **Pass:** No Auth Tokens, Account SIDs, API keys, or unintended real numbers in pack artifacts.

### A12 — Optional Jobber/HCP soft-fail
- **Steps:** If adapter enabled, force API failure (bad credential in Make only).
- **Pass:** Instant SMS and Follow_ups still succeed; failure noted; scenario does not hard-stop.

---

## Sign-off

| Role | Name | Date | Result |
|------|------|------|--------|
| Implementer | | | |
| Reviewer / PM | | | |

Attach QA_Log export or screenshot links in client folder (not in this pack).

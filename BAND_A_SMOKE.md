# Band A smoke — Make Free + Sheet intake

**Date:** 2026-09-21 (America/Chicago)  
**Scope:** Operational note for Free-plan Band A. No secrets.

## Make Free constraints

- **Max 2 active scenarios.**
- Free schedule minimum: **15 minutes** (Dispatcher must not use 10m on Free).

## Active pair (ON)

| Scenario | State | Path |
|----------|-------|------|
| LeadSpeed — 2 Web Form | ON | Sheet-only intake |
| LeadSpeed — 3 Manual Test | ON | Sheet-only intake |

## OFF (saved drafts)

| Scenario | State |
|----------|-------|
| LeadSpeed — 1 Missed Call (B) | OFF |
| LeadSpeed — 5 Inbound SMS | OFF |
| LeadSpeed — 4 Follow-up Dispatcher | OFF (15m schedule retained; activation OFF) |

## Twilio paused — Sheet-only

While Twilio Make connection / Send SMS is paused:

1. Webhook → NormalizePhone (E.164) → Google Sheets **Add a Row**.
2. `Lead_Log.status` = **`New`** (not `Texted`).
3. Leave Instant / alert SMS SIDs blank; skip Twilio modules.
4. Map `caller_phone` to the **evaluated** NormalizePhone / `e164` (or Tools Set variables) output.
   - **Never** write unevaluated formula literals such as `{{ concat(...) }}` into Sheets cells.
5. Use Sheets **Raw value** input for phone columns when mapping module outputs.

## Webhook placeholders

Live Make hooks rotate per environment. Prefer placeholders in docs:

| Scenario | Placeholder |
|----------|-------------|
| Web Form | `{{MAKE_WEBHOOK_WEBFORM}}` |
| Manual Test | `{{MAKE_WEBHOOK_MANUAL_TEST}}` |
| Missed Call (B) | `{{MAKE_WEBHOOK_MISSED_CALL_B}}` |
| Inbound SMS | `{{MAKE_WEBHOOK_INBOUND_SMS}}` |
| Follow-up Dispatcher | `{{MAKE_WEBHOOK_DISPATCHER}}` |

If you need the current environment URLs, copy from `SMOKE_WEBHOOKS.md` and treat them as **environment-specific — they rotate**.

## Proven smoke rows (examples, not secrets)

| Example lead_id | caller_phone | source | status |
|-----------------|--------------|--------|--------|
| `LEAD-20260921-QA02` | `+15555550100` | `manual_test` | `New` |
| `LEAD-20260921-WEB01` | `+15555550101` | `web_form` | `New` |

QA phones are reserved test numbers (`+1555555…`), not client PII.

## Related

- `SHEET_INTAKE_STATUS.md` — verification checklist
- `shared_modules.md` — NormalizePhone + Sheets mapping warning
- `03_Make_Scenario2_WebForm.json` / `04_Make_Scenario3_ManualTest.json` — `meta.import_notes`
- `05_Make_Scenario4_FollowupDispatcher.json` — 15m Free schedule note

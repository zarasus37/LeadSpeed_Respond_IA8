# Sheet intake status — 2026-09-21 (America/Chicago)

Band A / Make Free: Web Form + Manual Test **ON**; Twilio paused; Sheet-only path.

## Manual Test (Make)

- Scenario ON: webhook → NormalizePhone / Tools Set variables → Google Sheets Add a Row
- `caller_phone` maps to **evaluated** E.164 output (`caller_phone_e164` / NormalizePhone.e164) — Raw value input
- No Twilio modules
- Make Run once: success

## Web Form (Make)

- Scenario ON: same Sheet-only pattern; `source=web_form`
- No Twilio modules

## Proven smoke rows (examples, not secrets)

| lead_id | caller_phone | source | status |
|---------|--------------|--------|--------|
| `LEAD-20260921-QA02` | `+15555550100` | `manual_test` | `New` |
| `LEAD-20260921-WEB01` | `+15555550101` | `web_form` | `New` |

QA01 row (if present) may be left unchanged from earlier attempts; QA02 is the proven Manual Test pass.

## Mapping rule

- Map Sheets `caller_phone` to purple module output (evaluated E.164).
- Never free-type unevaluated `{{ concat }}` / formula literals into the Sheets field.
- status=`New` while Twilio paused (not `Texted`).

## Related

- `BAND_A_SMOKE.md`
- `SMOKE_WEBHOOKS.md` (placeholders preferred; staging URLs rotate)
- `shared_modules.md` (NormalizePhone + Sheets warning)

# Shared Make Modules — LeadSpeed Respond SKU #1

Implement these once as Make **subscenarios** (or reusable module groups) and call them from Scenarios 1–5. Do not re-invent per scenario.

---

## 1. NormalizePhone

**Purpose:** Convert any inbound phone string to E.164 US (`+1XXXXXXXXXX`). Reject if not a valid 10/11-digit US number.

**Inputs**

| Name | Type | Notes |
|------|------|-------|
| `raw_phone` | string | From Twilio `From` / `Caller`, form field, or test payload |

**Logic**

1. Strip spaces, dashes, parentheses, dots.
2. If starts with `+1` and length 12 → keep.
3. If 11 digits starting with `1` → prepend `+`.
4. If 10 digits → prepend `+1`.
5. Else → mark invalid.

**Outputs**

| Name | Type |
|------|------|
| `e164` | string \| empty |
| `is_valid` | boolean |
| `reject_reason` | string \| empty (`empty`, `non_us`, `too_short`, `too_long`, `invalid_chars`) |


**Sheets mapping warning (Band A / Sheet-only)**

When writing `Lead_Log.caller_phone` (or any phone column) from Make → Google Sheets:

- Map the cell to the **evaluated** NormalizePhone output: `e164` (or Tools → Set variables `caller_phone_e164`).
- **Never** paste unevaluated Make formula literals into Sheets (e.g. raw `{{ concat(...) }}`, `{{replace(...);}}` text that Sheets stores as a string).
- Prefer Sheets module **Raw value** input so the purple module-output token resolves at runtime to `+1XXXXXXXXXX`.
- Dedup and later Twilio sends assume `caller_phone` is already E.164; bad mapping breaks both.

**Used by:** Missed Call (B), Web Form, Manual Test, Inbound SMS, Follow-up Dispatcher (re-check).

---

## 2. IsPaused

**Purpose:** Gate all outbound SMS on `Config.system_paused`.

**Inputs:** none (reads Config)

**Logic**

1. Lookup Sheet `Config` where `key = system_paused`.
2. Treat `TRUE`, `true`, `1`, `yes` as paused.
3. If paused → stop scenario branch that would send SMS; optionally log.

**Outputs**

| Name | Type |
|------|------|
| `paused` | boolean |

**Used by:** Missed Call (B) before Instant SMS, Web Form, Manual Test, Follow-up Dispatcher, Inbound SMS (outbound replies only — STOP/HELP still processed).

---

## 3. Dedup

**Purpose:** Suppress duplicate Instant SMS for same `caller_phone` within `Config.dedup_window_minutes` (default **120**).

**Inputs**

| Name | Type |
|------|------|
| `caller_phone_e164` | string |
| `window_minutes` | number | From Config (default 120) |

**Logic**

1. Search `Lead_Log` for rows where `caller_phone` = input AND `created_at` ≥ now − window AND `opt_out` ≠ TRUE AND `status` ∉ (`Error` only if never texted — prefer any prior Instant).
2. Match if prior row has `instant_sms_sid` non-empty OR `status` in (`Texted`,`FollowUp`,`Replied`,`Won`,`Lost`).
3. If match → `is_duplicate = true`, return existing `lead_id`.

**Outputs**

| Name | Type |
|------|------|
| `is_duplicate` | boolean |
| `existing_lead_id` | string \| empty |

**Behavior on duplicate:** Do **not** send Instant SMS again. Optionally refresh `Lead_Log` note; do not schedule new Follow_ups. Owner alert optional (Config-driven; default skip alert on pure dedup).

**Used by:** Missed Call (B), Web Form, Manual Test.

---

## 4. ScheduleTouches

**Purpose:** Create Follow_ups rows for touches 1–3 with quiet-hours roll-forward.

**Inputs**

| Name | Type |
|------|------|
| `lead_id` | string |
| `timezone` | string | Default `America/Chicago` |
| `quiet_hours_start` | string | `08:00` |
| `quiet_hours_end` | string | `20:00` |
| `touch1_delay_minutes` | number | 30 |
| `touch2_delay_hours` | number | 24 |
| `touch3_delay_hours` | number | 72 |

**Logic**

1. Base time = now (America/Chicago).
2. Compute raw `send_at`:
   - Touch 1: now + touch1_delay_minutes
   - Touch 2: now + touch2_delay_hours
   - Touch 3: now + touch3_delay_hours
3. For each: if local time < quiet_hours_start → roll to same day at start; if ≥ quiet_hours_end → roll to next day at quiet_hours_start.
4. Insert 3 rows into `Follow_ups`: `status=Pending`, `sms_sid` empty, `skip_reason` empty.
5. Generate `followup_id` = `FU-{lead_id}-{touch}` (or UUID).

**Outputs**

| Name | Type |
|------|------|
| `followup_ids` | array of 3 strings |

**Cancel rules (callers elsewhere):** On Replied / OptOut / Won / Lost / system pause → set all Pending Follow_ups for lead to `Canceled` with `skip_reason`.

**Used by:** Missed Call (B), Web Form, Manual Test — only after successful Instant SMS.

---

## 5. ErrorHandler

**Purpose:** Uniform failure path — log + owner alert, never silent fail.

**Inputs**

| Name | Type |
|------|------|
| `context` | string | Scenario name + step |
| `lead_id` | string \| empty |
| `error_detail` | string |
| `alert_owner` | boolean | Default true |

**Logic**

1. If `lead_id` present → update `Lead_Log.status=Error`, set `error_detail`.
2. Else → append QA_Log or a minimal Lead_Log Error row if phone known.
3. If `alert_owner` → Twilio SMS to `Config.owner_phone` using `Message_Slots.alert_owner` pattern or fixed: `[LeadSpeed] Error in {{context}}: {{error_detail}}` (keep under 320 chars).
4. Do not retry endlessly; Make error handler: 1 retry then ErrorHandler.

**Outputs:** none (side effects only)

**Used by:** All scenarios on module failure, invalid phone (Web Form), Twilio send failure.

---

## Quiet hours helper (inline, not separate module)

Used inside ScheduleTouches and optionally Follow-up Dispatcher re-check:

```
function rollQuiet(sendAt, tz, start, end):
  local = convert(sendAt → tz)
  if local.time < start: local.date + start
  else if local.time >= end: local.date+1 + start
  else: local
  return local as ISO8601
```

---

## Merge variables (global)

Available to Message_Slots rendering:

- `{{business_name}}`
- `{{caller_name}}` (or "there" if blank)
- `{{caller_phone}}`
- `{{service_interest}}`
- `{{form_message}}` (truncated)
- `{{owner_phone}}`
- `{{tech_phone}}`
- `{{lead_id}}`
- `{{source}}`

STOP/HELP keywords: case-insensitive exact `STOP`, `STOPALL`, `UNSUBSCRIBE`, `CANCEL`, `END`, `QUIT` → OptOut; `HELP`, `INFO` → help_reply.

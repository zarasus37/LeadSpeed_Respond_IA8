# 13 — Config Keys Reference

Sheet tab **Config**: columns `key`, `value`, `notes`.  
All phones **E.164** (`+1XXXXXXXXXX`). No API keys in this Sheet.

| key | type | default | meaning |
|-----|------|---------|---------|
| `business_name` | string | _(client)_ | Display name merged into SMS |
| `owner_phone` | e164 | _(client)_ | Owner mobile — Dial target + owner alerts |
| `tech_phone` | e164 | _(client)_ | Tech/on-call mobile for optional alerts |
| `alert_tech` | boolean | `TRUE` | If TRUE, send `alert_tech` SMS on Instant |
| `twilio_from_number` | e164 | _(client)_ | Twilio sender / business SMS number |
| `timezone` | IANA | `America/Chicago` | Quiet hours + schedule interpretation |
| `quiet_hours_start` | HH:MM | `08:00` | Inclusive local start for lead SMS |
| `quiet_hours_end` | HH:MM | `20:00` | Exclusive local end; roll to next start |
| `touch1_delay_minutes` | integer | `30` | Minutes after Instant → Touch 1 |
| `touch2_delay_hours` | integer | `24` | Hours after Instant → Touch 2 |
| `touch3_delay_hours` | integer | `72` | Hours after Instant → Touch 3 |
| `dedup_window_minutes` | integer | `120` | Suppress duplicate Instant for same phone |
| `system_paused` | boolean | `FALSE` | TRUE blocks outbound Instant + touches |
| `jobber_enabled` | boolean | `FALSE` | Optional Jobber adapter (soft-fail) |
| `hcp_enabled` | boolean | `FALSE` | Optional Housecall Pro adapter (soft-fail) |

## Boolean parsing

Treat as TRUE: `TRUE`, `true`, `1`, `yes` (case-insensitive).  
Everything else → FALSE.

## Change control

- Delays / dedup / quiet hours: change only with operator + client agreement; update QA.
- `system_paused`: client may toggle (see Runbook).
- Enabling Jobber/HCP: requires adapter setup in `14_Optional_Jobber_HCP_Adapter.md` — never blocks core SMS.

# LeadSpeed Band A smoke webhooks

**WARNING — environment-specific.** Live Make hook URLs rotate when scenarios are recreated or hooks are regenerated. Prefer placeholders in shared docs. Do not treat URLs below as permanent or as secrets vault material; they are staging hook paths only.

Build status: Dispatcher saved; Free-plan swap completed in Make us2 (Band A) on Sep 21, 2026 (America/Chicago).

## Preferred placeholders (use in new docs)

| Scenario | Placeholder | Band A state |
|----------|-------------|--------------|
| LeadSpeed — 1 Missed Call (B) | `{{MAKE_WEBHOOK_MISSED_CALL_B}}` | OFF |
| LeadSpeed — 5 Inbound SMS | `{{MAKE_WEBHOOK_INBOUND_SMS}}` | OFF |
| LeadSpeed — 2 Web Form | `{{MAKE_WEBHOOK_WEBFORM}}` | ON |
| LeadSpeed — 3 Manual Test | `{{MAKE_WEBHOOK_MANUAL_TEST}}` | ON |
| LeadSpeed — 4 Follow-up Dispatcher | `{{MAKE_WEBHOOK_DISPATCHER}}` | OFF draft |

## Current staging hooks (rotate — copy only if needed)

| Scenario | Status | Staging hook (environment-specific) |
|---|---|---|
| LeadSpeed — 1 Missed Call (B) | OFF | `https://hook.us2.make.com/iw7enjoqeyml8e8vw6tpuk8251pjbgk6` |
| LeadSpeed — 5 Inbound SMS | OFF | `https://hook.us2.make.com/3emjqvqekr0ai79bdqhkivc2hk7ytu3r` |
| LeadSpeed — 2 Web Form | ON | `https://hook.us2.make.com/p2tolchwxdbb48b9esofvq2iaj7rmq88` |
| LeadSpeed — 3 Manual Test | ON | `https://hook.us2.make.com/47kc0p198jdwoep34m1nljbj8dpcucdg` |
| LeadSpeed — 4 Follow-up Dispatcher | OFF draft | `https://hook.us2.make.com/1yuj3zb7km16ol9t6yvbt4c1k53ret26` |

Dispatcher uses a Custom Webhook trigger and retains the **Every 15 minutes** schedule setting with activation OFF (Make Free minimum). Web Form and Manual Test were verified as webhook → NormalizePhone → Sheets intake drafts **without** Twilio Send SMS modules before activation.

Twilio Make connection: not created for this Band A pass; no Twilio credentials in pack.  
Google Sheets: Sheet-only intake proven (see `SHEET_INTAKE_STATUS.md` / `BAND_A_SMOKE.md`).

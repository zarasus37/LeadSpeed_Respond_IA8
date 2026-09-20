# 14 — Optional Jobber / Housecall Pro Adapter (soft-fail only)

**Status:** Optional. Not required for SKU #1 go-live.  
**Rule:** CRM failures must **never** block Instant SMS, alerts, or Follow_ups scheduling.

---

## When to enable

- Client already on Jobber or Housecall Pro (HCP).
- Config `jobber_enabled=TRUE` **or** `hcp_enabled=TRUE` (not both unless explicitly dual-writing — default pick one).

---

## Placement in Make

After successful Instant SMS + Lead_Log `Texted` + ScheduleTouches (Scenario 1/2/3 module “optional:JobberOrHCP”):

1. Router: if neither flag → skip.
2. HTTP / native Make app: create/update client + optional request/job with lead phone, name, note (`source`, `lead_id`, form snippet).
3. On **success:** write `external_id` on Lead_Log.
4. On **failure:** append to `error_detail` (e.g. `jobber_soft_fail: timeout`) or a non-blocking note field; **continue scenario as success**.

Use Make error handler on the CRM module: “Resume / Ignore” → log → do not throw to scenario failure.

---

## Suggested payload (logical)

```
{
  "first_name": "{{caller_name or 'Lead'}}",
  "phone": "{{caller_phone}}",
  "notes": "LeadSpeed {{source}} {{lead_id}} {{service_interest}} {{form_message}}",
  "source_tag": "leadspeed_respond"
}
```

Map to Jobber Clients / Requests or HCP customers / jobs per current API. Exact field names change — implementer verifies against live API docs at setup time. **No API keys in this file** — store in Make connection only (`{{JOBBER_API_KEY}}` style placeholders in Make UI).

---

## Soft-fail acceptance (QA A12)

| Step | Expected |
|------|----------|
| Force 401/500 from CRM | Instant SMS still delivered; Follow_ups Pending created |
| Lead_Log | `status` remains Texted/FollowUp; soft-fail noted |
| Client | Optional owner note only if Config wants CRM alerts — default **no** extra SMS spam |

---

## Out of scope

- Two-way deep sync, quoting, invoicing, QuoteChase, ReviewBoost
- Blocking lead SMS on CRM validation errors
- Storing CRM tokens in Google Sheets

---

## Disable

Set `jobber_enabled` and `hcp_enabled` to `FALSE`. Leave modules disconnected or router-skipped.

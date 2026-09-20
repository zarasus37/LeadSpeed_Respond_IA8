# 11 — Client Runbook (IA7 §8)

LeadSpeed Respond — day-to-day operations for the business owner / office manager.

---

## 1. What the system does

When a caller misses you (or submits your web form), LeadSpeed texts them back quickly, alerts you, and sends up to three follow-up texts (30 minutes, 24 hours, 72 hours) unless they reply, opt out, or you mark the lead Won/Lost.

**Quiet hours:** Texts to leads only between **8:00 AM and 8:00 PM America/Chicago**. Outside that window, sends roll to the next morning.

---

## 2. What you will receive

- **Owner alert SMS** when a new Instant text-back fires (missed call or form).
- **Tech alert SMS** if enabled in setup.
- **Reply alert** when the lead texts back something other than STOP/HELP.

---

## 3. Google Sheet — your control panel

| Tab | You use it to… |
|-----|----------------|
| **Config** | Pause system, update phones, quiet hours (ask us before changing delays) |
| **Lead_Log** | See every lead, status, errors |
| **Follow_ups** | See scheduled / sent / canceled touches |
| **Message_Slots** | View copy (changes go through Writer / us) |
| **QA_Log** | Support/test history |

### Pause all outbound texts

1. Open **Config**.
2. Set `system_paused` to `TRUE`.
3. Set back to `FALSE` to resume.

### Mark Won / Lost

In **Lead_Log**, set `status` to `Won` or `Lost`. Pending follow-ups will cancel on the next dispatcher pass (or ask us to cancel immediately).

---

## 4. Lead replies & STOP

| Lead texts | What happens |
|------------|--------------|
| Normal reply | Status → Replied; follow-ups stop; you get an alert |
| STOP | Opt-out confirmed; no more marketing/follow-up texts from this system |
| HELP | Automated help text with how to reach you |

---

## 5. Common issues

| Symptom | Try this |
|---------|----------|
| No text after missed call | Confirm call was unanswered; check `system_paused`; check Lead_Log Error |
| Duplicate texts | Dedup window is 2 hours — tell us if still duplicating |
| Texts at wrong time | Confirm timezone `America/Chicago` and quiet hours in Config |
| Want copy changed | Request Writer update to Message_Slots — do not invent live copy ad hoc |

---

## 6. What we handle on retainer

Monitoring, incident fixes, copy updates coordination, light Config changes, monthly health check — see `12_Retainer_Checklist.md`.

**Not included:** New products (QuoteChase, ReviewBoost, ads, IVR, AI chatbots), deep CRM projects.

---

## 7. Escalation

1. Note `lead_id` and time (America/Chicago).
2. Screenshot Lead_Log row + any SMS.
3. Contact your LeadSpeed operator (channel set at handoff).

---

## 8. Compliance reminder

Leads can opt out with STOP. Do not import purchased lists into this Sheet. Use only leads that contacted your business.

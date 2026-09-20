# 12 — Retainer Checklist (IA7 §9)

**Retainer:** $497/mo · **Budget:** 2–3 hours/month  
**SKU:** LeadSpeed Respond #1 (Make + Twilio + Sheets)

Perform monthly (or after any incident). Log notable findings for the client.

---

## Monthly health (≈60–90 min)

- [ ] Make scenario history: failures last 30 days — triage ErrorHandler spikes
- [ ] Twilio: messaging geo permissions, undelivered, opt-out list sanity
- [ ] Lead_Log: sample 10 rows — statuses coherent; Error rate acceptable
- [ ] Follow_ups: no stuck Pending older than touch3 window without skip/cancel reason
- [ ] Config: `system_paused=FALSE` unless client requested pause; phones still valid E.164
- [ ] Quiet hours still 08:00–20:00 America/Chicago (or client-agreed change documented)
- [ ] Dedup window still 120 unless documented change
- [ ] Webhooks A/B + inbound SMS URL still match Twilio console
- [ ] Message_Slots: no accidental `[WRITER:` leftover after copy updates
- [ ] Latency spot-check: one Manual Test — Instant <60s

## Optional soft-fail adapters (if enabled)

- [ ] Jobber/HCP: success rate; failures must not block SMS (confirm last soft-fail note)

## Light ops (remainder of 2–3h)

- [ ] Apply approved copy tweaks from Writer
- [ ] Small Config changes (owner/tech phone, alert_tech)
- [ ] Re-run failed QA gates after fixes
- [ ] Brief client note: volume (leads), reply rate, opt-outs, incidents

## Out of retainer (quote separately)

- New SKUs / QuoteChase / ReviewBoost
- Deep CRM, IVR, AI chatbot, ads
- Number porting projects beyond minor Twilio tweaks
- Rebuild on a different stack

## Incident SLA (informal retainer norm)

- Acknowledge same business day when paused or Instant down
- Restore Instant path priority over copy polish

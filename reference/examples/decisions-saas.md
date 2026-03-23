# Architecture Decisions

## Active

### Feb 12, 2026 — Enrichment First
**Type:** decision
**Summary:** Inference depends on reliable data. Build the telematics pipeline before pattern detection.
**EIID Layer:** enrichment
**Action:** Prioritized enrichment connectors (telematics polling, email receipt parsing, WhatsApp media intake) over inference and interpretation.

### Feb 14, 2026 — Per-Fleet Batching for Telematics
**Type:** decision
**Summary:** Telematics API rate limits require batching. One Inngest cron per fleet, not per vehicle.
**EIID Layer:** enrichment
**Action:** Built inngest/functions/poll-telematics.ts with per-fleet batching.

### Feb 19, 2026 — Fleet Filter Is Not Scope Creep
**Type:** alignment-check
**Summary:** Fleet overview filter component added. Fleet managers need to filter by region, vehicle type, alert status. Maps to interpretation layer — visualizations that don't fit in a message.
**EIID Layer:** interpretation
**Action:** None needed, aligned.

### Feb 21, 2026 — No Custom Email Templates
**Type:** drift-warning
**Summary:** Custom email template builder started. Email delivery is commodity — Brevo handles templates. Building custom templates is scope creep: it differentiates in the wrong layer. The value is in what the email says (interpretation), not how it's rendered (delivery).
**EIID Layer:** none
**Action:** Removed. Using Brevo transactional templates. Saved 3 days.

### Feb 24, 2026 — Engine Temperature Prediction Opportunity
**Type:** opportunity
**Summary:** Telematics data includes engine temperature. Combined with weather data, could predict cooling system failures 2-3 weeks early. No competitor does this for mid-market fleets. This is the kind of cross-source correlation (enrichment → inference) that justifies the "innovate" classification.
**EIID Layer:** inference
**Action:** Added to backlog. Requires weather API integration (enrichment) first.

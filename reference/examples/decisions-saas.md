# Architecture Decisions

### Feb 12, 2026 — Strategic Priorities
**Type:** decision
**Summary:** Focus on enrichment pipeline first. Inference depends on having reliable data.
**Automate:** Auth (Supabase Auth), hosting (Vercel), email (Brevo)
**Differentiate:** Maintenance scheduling — fleet manager sets priority, system surfaces data
**Build:** Telematics + maintenance + weather correlation pipeline

### Feb 14, 2026 — Telematics Polling Architecture
**Type:** decision
**Summary:** Inngest cron every 5 min per fleet. Batch vehicles to avoid API rate limits.
**EIID Layer:** enrichment
**Action:** Built inngest/functions/poll-telematics.ts with per-fleet batching

### Feb 19, 2026 — Alignment Check
**Type:** alignment-check
**Summary:** Fleet overview filter component added. Not scope creep — fleet managers need to filter by region, vehicle type, alert status. Maps to interpretation layer (visualizations that don't fit in a message).
**EIID Layer:** interpretation
**Action:** None needed, aligned

### Feb 21, 2026 — Drift Warning
**Type:** drift-warning
**Summary:** Custom email template builder started. Email delivery is commodity (Brevo handles templates). Building custom templates is scope creep.
**EIID Layer:** none
**Action:** Removed. Using Brevo transactional templates instead. Saved 3 days.

### Feb 24, 2026 — Opportunity Detected
**Type:** opportunity
**Summary:** Telematics data includes engine temperature. Combined with weather data, could predict cooling system failures 2-3 weeks early. No competitor does this for mid-market fleets.
**EIID Layer:** inference
**Action:** Added to backlog. Requires weather API integration (enrichment) first.

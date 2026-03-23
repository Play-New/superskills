# FleetPulse — Build Plan

Example build plan produced by `/super:build` init mode for a visual-heavy SaaS product. All components are code or buy — no agents, no workflows, no LLM calls.

## Readiness

- **Strategy gate:** PASS — CLAUDE.md has EIID mapping with Approach fields on all layers
- **Stack gate:** PASS — Next.js 15, Supabase, Inngest, shadcn + Tailwind v4 installed
- **Design gate:** PASS — design-system.md has Direction, Typography (IBM Plex Mono + Geist), Tokens (Nova preset), Layout (sidebar + content)

## Build Order

### Shared Standards

| # | Piece | Type | Dependencies |
|---|-------|------|--------------|
| 1 | Project scaffold | shared-standard | none |
| 2 | Database schema — vehicles, alerts, maintenance, routes | shared-standard | #1 |
| 3 | Auth — Supabase Auth, middleware, protected routes | shared-standard | #1 |
| 4 | Layout shell — 240px sidebar, Fleet/Alerts/Maintenance/Routes/Reports nav | shared-standard | #3 |
| 5 | Settings — polling frequency, alert thresholds, notification channels, user roles | shared-standard | #3, #4 |
| 6 | Error handling — structured API errors, error boundaries | shared-standard | #1 |
| 7 | Logging — structured JSON, request logging middleware | shared-standard | #1 |

### EIID Layers

| # | Piece | Type | EIID Layer | Approach | Dependencies |
|---|-------|------|------------|----------|--------------|
| 8 | Telematics polling — Inngest cron, per-fleet batching | unique | Enrichment | Automate via buy (cron) + code (normalization) | #2, #7 |
| 9 | Email receipt parsing — incoming email webhook, extract maintenance data | unique | Enrichment | Automate via code | #2, #7 |
| 10 | WhatsApp media intake — driver photos and voice notes | unique | Enrichment | Automate via buy (WhatsApp API) | #2, #7 |
| 11 | Fuel efficiency baseline — per-vehicle historical average, deviation detection | unique | Inference | Innovate via code | #8 |
| 12 | Maintenance prediction — mileage + service history + vehicle age | unique | Inference | Innovate via code | #8, #9 |
| 13 | Idle time anomaly — stationary during work hours detection | unique | Inference | Innovate via code | #8 |
| 14 | Insight formatting — comparison to fleet average, 4-week trend, action recommendation | unique | Interpretation | Differentiate via code | #11, #12, #13 |
| 15 | WhatsApp alerts — business hours only, attention format | unique | Delivery | Automate via buy | #14 |
| 16 | Email daily summary — template, 3-line format | unique | Delivery | Automate via buy | #14 |

### Visual Surfaces

| # | Piece | Type | Screen | Dependencies |
|---|-------|------|--------|--------------|
| 17 | Fleet Overview — attention count, fleet map, active/idle split, vehicle list | unique | Fleet Overview | #4, #11, #12, #13, #14 |
| 18 | Vehicle Detail — status badge, location, service date, maintenance history tab, fuel trend | unique | Vehicle Detail | #17 |
| 19 | Alerts — unacknowledged count, active list, severity breakdown, timeline | unique | Alerts | #4, #14 |
| 20 | Maintenance — upcoming service, overdue count, schedule, service history | unique | Maintenance | #4, #12 |
| 21 | Reports — fleet health score, fuel efficiency trend, alert frequency, custom range | unique | Reports | #4, #14 |

### Background Jobs

| # | Piece | Type | Dependencies |
|---|-------|------|--------------|
| 22 | Inngest functions — telematics poll, alert evaluation, daily summary trigger | unique | #8, #11, #15, #16 |

## Acceptance Criteria (examples)

**#3 Auth:**
- Unauthenticated GET /dashboard returns redirect to /login
- POST /api/vehicles without session returns 401
- Login with Supabase credentials creates session, redirects to /dashboard
- Middleware covers all /app/* and /api/* routes

**#11 Fuel efficiency baseline:**
- Given 30 days of fuel data for vehicle VH-042, calculates baseline average
- When new reading deviates >15% from baseline, creates alert record
- Alert record includes vehicle ID, deviation percentage, duration

**#17 Fleet Overview:**
- Attention count shows number of vehicles with active alerts
- Fleet map renders with vehicle positions from latest telematics data
- Vehicle list is sortable by status, fuel efficiency, next service
- Uses design tokens: 36px table rows, 8px cell padding, amber-500 for alerts
- Responsive: table scrolls horizontally on sm breakpoint

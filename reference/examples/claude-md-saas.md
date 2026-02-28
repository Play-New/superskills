# FleetPulse

## Business
**Client:** LogiTrack GmbH
**Industry:** Logistics, mid-market, DACH region

## User
**End user:** Fleet manager, 30-80 vehicles, checks dashboard between site visits
**Need:** Know which vehicles need attention before breakdowns happen, not after

## Stack
Next.js 15 (App Router), Supabase (database + auth + realtime), Inngest (scheduled jobs), Vercel (hosting), shadcn + Tailwind v4

## EIID

### Enrichment (data)
**Have:** Telematics API (GPS, fuel, mileage), maintenance records in Supabase
**Missing:** Weather data for route conditions, tire pressure from OBD-II
**Connect:** Telematics API polling every 5 min via Inngest cron
**Approach:** Automate — telematics polling is commodity (Inngest cron). Weather API is commodity (public APIs). OBD-II integration is differentiate (hardware-dependent, needs fleet manager input on which vehicles).

### Inference (patterns)
**Detect:** Vehicles deviating >15% from fuel efficiency baseline
**Predict:** Maintenance window based on mileage + last service + vehicle age
**Flag:** Idle time anomalies (vehicle stationary during work hours)
**Approach:** Innovate — cross-source correlation (telematics + maintenance + weather = predictive insight). No off-the-shelf product does this for mid-market fleets.

### Interpretation (insights)
**Surface:** "Vehicle DE-4521 is 800km from next service and fuel efficiency dropped 18% this week"
**Frame as:** Comparison to fleet average + trend over 4 weeks + recommended action
**Approach:** Differentiate — maintenance scheduling needs fleet manager judgment on priority. System provides data, human decides.

### Delivery (reach)
**Channels:** Dashboard (primary), WhatsApp (urgent), email (weekly digest)
**Triggers:** Efficiency drop > 10%, maintenance window < 500km, idle > 2h
**Timing:** WhatsApp alerts during business hours only (7-19 CET)
**Approach:** Automate — auth (Supabase Auth), hosting (Vercel), email delivery (Brevo), WhatsApp delivery (Brevo). All commodity.

## Technology Constraints
Use Supabase, NOT Firebase, AWS Amplify.
Use Vitest, NOT Jest, Mocha.
Use Tailwind CSS, NOT styled-components, Emotion.
Use pnpm, NOT npm, yarn, bun.

## Code Architecture
- No source file over 200 lines. Split by responsibility.
- One component per file. One utility per file.
- Colocation: tests next to source, types next to usage.
- Prefer composition over inheritance.
- If a module has two distinct modes, split into separate files.

## Design System
**Framework:** shadcn + Tailwind v4
**Style:** Nova (compact, data-heavy)
**Token source:** globals.css (OKLCH)
**Direction:** Dense and operational — tight spacing, monospace data, slate palette with amber alerts
**Navigation:** Sidebar — Fleet, Alerts, Maintenance, Routes, Reports
**Typography:** IBM Plex Mono (data) + Geist (body)
**Color character:** Industrial calm. Slate surfaces, amber when something needs attention, emerald when healthy. Red reserved for actual emergencies.
**Signature:** The attention count — a single amber number at the top of every screen showing vehicles needing action right now.

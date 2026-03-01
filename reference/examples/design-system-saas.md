# Design System

## Direction
**Feel:** Operational control room. Dense information, calm surfaces, amber sparks when attention is needed.
**Domain concepts:** Fleet, route, alert threshold, maintenance window, duty cycle, fuel baseline
**Color world:** Asphalt gray, diesel amber, dashboard green, hazard red. The palette of a truck yard at 6am.
**Signature:** The attention count — a single amber number at the top of every screen showing vehicles needing action right now. No other fleet tool leads with this.
**Rejected defaults:** Rounded consumer app look, blue-heavy SaaS palette, card-heavy dashboard with big spacing

## Information Architecture
**Navigation:** Sidebar — Fleet, Alerts, Maintenance, Routes, Reports. Settings at sidebar bottom. User profile in avatar menu.
**Excluded from nav:** Integrations (inside Settings), Export (inside Reports), Vehicle Detail (drill-down from Fleet)

Primary input happens outside the interface: drivers send WhatsApp voice notes and photos, managers forward email receipts. Primary alerts are delivered on WhatsApp and email. The web interface shows visualizations that don't fit in a message and configuration for the invisible layer.

| Screen | Focal Point | Above the Fold | One Click | Deep |
|--------|-------------|----------------|-----------|------|
| Fleet Overview | Vehicles needing attention count | Attention count, fleet map, today's active/idle split | Vehicle list with sort/filter, mileage leaders | Bulk actions, export fleet data |
| Vehicle Detail | Status badge | Status badge, current location, next service date | Maintenance history tab, fuel trend chart, trip log | Edit vehicle info, assign to route, decommission |
| Alerts | Unacknowledged count | Unacknowledged count, active alerts list, severity breakdown | Alert timeline, acknowledged history | Alert rule configuration, notification channels |
| Maintenance | Upcoming service list | Upcoming service list, overdue count, this week's schedule | Service history per vehicle, cost breakdown | Service provider management, parts inventory |
| Reports | Fleet health score | Fleet health score, fuel efficiency trend, alert frequency | Custom date range, vehicle comparison, route analysis | Report builder, scheduled reports, export templates |
| Settings | — | Enrichment sources + polling frequency, delivery channel config | Inference prompts, detection thresholds | User management, roles, cron schedules, integrations |

**Content depth rules:**
- Surface: interpretation visualizations — live positions on map, fleet trends, attention-needing vehicles. Things a WhatsApp message can't show.
- One click: context — history tabs, trend charts, timelines, comparisons that explain the current state
- Deep: configuration — enrichment sources, inference prompts, alert rules, delivery channels, user roles, cron schedules

## Tokens
**Spacing base:** 4px
**Scale:** 4, 8, 12, 16, 24, 32
**Radius:** 4px (inputs, cards), 2px (badges, tags), 0 (tables)
**Depth strategy:** Borders only. Thin 1px slate-200 borders, no shadows except modals.
**Color primitives:**
- Foreground: slate-900 (primary), slate-600 (secondary), slate-400 (muted)
- Surfaces: white (base), slate-50 (panel), slate-100 (recessed)
- Accent: amber-500 (alerts), emerald-500 (healthy), red-500 (critical)
- Data: blue-600 (charts primary), violet-500 (charts secondary)

## Component Patterns

| Component | Height | Padding | Radius | Notes |
|-----------|--------|---------|--------|-------|
| Button (default) | 32px | 8px 12px | 4px | Compact for toolbar density |
| Button (large) | 40px | 12px 16px | 4px | Primary actions only |
| Input | 32px | 8px 12px | 4px | Matches button height |
| Badge | 20px | 2px 6px | 2px | Status indicators |
| Card | auto | 16px | 4px | 1px border, no shadow |
| Table row | 36px | 8px 12px | 0 | Alternating slate-50 rows |
| Modal | auto | 24px | 8px | Only element with shadow |

## Decisions
| Decision | Why | Date |
|----------|-----|------|
| Nova style preset | Fleet managers want density, not whitespace. 30+ vehicles on one screen. | Feb 2026 |
| Borders only, no shadows | Control room feel. Shadows add visual noise when data is the focus. | Feb 2026 |
| IBM Plex Mono for data | Vehicle IDs, mileage, fuel numbers need monospace alignment. Plex pairs well with Geist. | Feb 2026 |
| Amber for alerts, not red | Red is reserved for critical (breakdown, accident). Most alerts are "attention needed", not emergencies. | Feb 2026 |

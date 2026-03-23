# Design System

## Direction
**Feel:** Operational control room. Dense information, calm surfaces, amber sparks when attention is needed.
**Domain concepts:** Fleet, route, alert threshold, maintenance window, duty cycle, fuel baseline
**Color world:** Asphalt gray, diesel amber, dashboard green, hazard red. The palette of a truck yard at 6am.
**Signature:** The attention count — a single amber number at the top of every screen showing vehicles needing action right now. No other fleet tool leads with this.
**Rejected defaults:** Rounded consumer app look, blue-heavy SaaS palette, card-heavy dashboard with big spacing

## Experience Patterns

**Feedback:** Visual surfaces — interactive elements respond in <100ms. Table row hover highlights the row. Alert acknowledgment shows a brief checkmark before the row moves to history. WhatsApp — photo receipt confirmation arrives within 5 seconds of upload. Email — daily summary subject line IS the key metric ("FleetPulse Daily — 3 attention, 2 overdue").
**Pacing:** Operational tool — answer first, detail on demand. The dashboard leads with the attention count (headline), not a loading animation. WhatsApp alerts lead with severity and vehicle count, context follows. CLI/API responses return the number first, breakdown second.
**Voice and tone:** Terse, factual, no filler. "3 vehicles need attention" not "We noticed some vehicles that may require your attention." Same voice in dashboard labels, WhatsApp messages, email subjects, and error messages. The product speaks like a competent dispatcher.
**Gratification:** Subtle. When all vehicles are healthy, the attention count shows "0" in green — a quiet win. WhatsApp daily summary leads with the positive ("Fleet running clean today") when there are no alerts. No confetti, no celebration animations. The gratification is absence of problems.
**Restraint:** No toast notifications for routine events (vehicle check-in, successful sync). No WhatsApp messages for resolved alerts (silence IS the resolution signal). No confirmation dialogs for reversible actions (acknowledge alert, dismiss notification). Dashboard shows no empty states with illustrations — an empty alert list just says "No active alerts."

## EIID Interface Map

| Layer | Modality | Surface | Rationale |
|-------|----------|---------|-----------|
| Enrichment | mixed | WhatsApp photos + voice notes, email forwarding, telematics API | Drivers contribute through WhatsApp (conversational). Telematics polling is automated. API keys and polling config need a visual settings surface. |
| Inference | visual | web dashboard | Pattern detection rules and thresholds are user-configurable. Anomaly monitoring needs a real-time visual dashboard. |
| Interpretation | visual | web dashboard + WhatsApp/email | Charts, maps, and trend comparisons require a visual surface. Headlines (attention count, fleet health score) also delivered conversationally. |
| Delivery | mixed | WhatsApp + email + web | Urgent alerts go to WhatsApp and email (conversational). Full context and drill-down live on the web dashboard (visual). |

**Visual scope:** Inference (configuration + monitoring), Interpretation (charts, maps, trends), and Delivery (drill-down detail, configuration) need graphical interfaces. This is a visual-heavy product.
**Non-visual scope:** Enrichment input is primarily conversational (drivers on WhatsApp). Delivery alerts go to WhatsApp and email. These channels need message structure design alongside the visual dashboard.

## References
**Borrowed:** Tight sidebar density from Linear (240px fixed, minimal chrome). Amber-on-dark attention signal from industrial control panels. Monospace data alignment from Bloomberg terminal aesthetic.
**Avoided:** Generic card grid dashboards (every SaaS looks the same). Low-contrast gray text on white (unreadable at 6am). Blue-heavy palettes (no domain connection to fleet operations).
**Assets:** None. Greenfield project.

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

## Layout
**Grid:** 12-column on lg+ (1024px), single column on sm. 16px gutter, 24px on lg+.
**Breakpoints:** sm 640px, md 768px, lg 1024px, xl 1280px
**Container:** fluid with 16px edge padding. No max-width on dashboard screens. 720px max for forms and settings.
**Page patterns:**

| Pattern | Structure | Used on |
|---------|-----------|---------|
| Sidebar + content | 240px fixed sidebar, fluid content area | All screens |
| Full-width table | Content fills available width, horizontal scroll on sm | Fleet Overview, Alerts |
| Split panel | 60/40 split, master list left, detail right | Vehicle Detail |
| Constrained form | Centered 720px max, generous vertical spacing | Settings |
| Dashboard grid | 2-3 column card grid on lg, stacked on sm | Reports |

## Typography Scale

| Level | Size | Weight | Line Height | Tracking | Usage |
|-------|------|--------|-------------|----------|-------|
| Display | 36px | 600 | 1.1 | -0.02em | Page heroes, fleet health score |
| H1 | 24px | 600 | 1.2 | -0.01em | Page titles |
| H2 | 18px | 600 | 1.3 | 0 | Section headings |
| H3 | 15px | 500 | 1.4 | 0 | Card titles, group labels |
| Body | 14px | 400 | 1.5 | 0 | Default text, descriptions |
| Small | 13px | 400 | 1.4 | 0 | Secondary text, table cells |
| Caption | 11px | 500 | 1.3 | 0.02em | Labels, metadata, timestamps |
| Mono | 13px | 400 | 1.4 | 0 | Vehicle IDs, mileage, fuel numbers |

## Composition
**Hierarchy:** three weight levels — 600 for primary (page title, focal metric), 500 for secondary (section heads, card titles), 400 for tertiary (body, metadata). Size reinforces: primary is 24-36px, secondary 15-18px, tertiary 11-14px. Hierarchy reads without color.
**Density map:** tables and fleet lists are dense (36px rows, 8px cell padding). Key metrics are generous (48px vertical padding around the attention count). Settings forms are medium (16px between fields, 32px between groups).
**Section rhythm:** 32px between major sections, 16px between groups within a section, 8px between items within a group.
**Proportion:** focal element (attention count, fleet health score) is 2-3x the size of supporting content. Dashboard cards are equal weight to each other but subordinate to the focal metric.
**Whitespace:** 16px page margins (24px on lg+). Grouping gaps (8px) are visibly smaller than separating gaps (32px). No section runs edge-to-edge except the sidebar.

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

## Conversational Patterns

### WhatsApp Alert (fleet manager)
```
*attention* 3 vehicles need action
VH-042: fuel 23% below baseline (3 days)
VH-017: overdue service by 480km
VH-089: idle >4 hours, engine running
→ Open dashboard for details
```

### WhatsApp Driver Input (confirmation)
```
Got it. Photo logged for VH-042.
Fuel receipt: 68L at Shell Bergamo, EUR 1.42/L.
Next service in 2,400km.
```

### Email Alert (daily summary)
```
Subject: FleetPulse Daily — 3 attention, 2 overdue

3 vehicles need attention today.
2 maintenance services overdue.
Fleet fuel efficiency: 8.2 km/L (down 4% this week).

Top priority: VH-042 fuel anomaly (3 consecutive days below baseline).

→ Open FleetPulse dashboard
```

## Decisions
| Decision | Why | Date |
|----------|-----|------|
| Nova style preset | Fleet managers want density, not whitespace. 30+ vehicles on one screen. | Feb 2026 |
| Borders only, no shadows | Control room feel. Shadows add visual noise when data is the focus. | Feb 2026 |
| IBM Plex Mono for data | Vehicle IDs, mileage, fuel numbers need monospace alignment. Plex pairs well with Geist. | Feb 2026 |
| Amber for alerts, not red | Red is reserved for critical (breakdown, accident). Most alerts are "attention needed", not emergencies. | Feb 2026 |

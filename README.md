```
 ____                       ____  _    _ _ _
/ ___| _   _ _ __   ___ _ _/ ___|| | _(_) | |___
\___ \| | | | '_ \ / _ \ '__\___ \| |/ / | | / __|
 ___) | |_| | |_) |  __/ |  ___) |   <| | | \__ \
|____/ \__,_| .__/ \___|_| |____/|_|\_\_|_|_|___/
             |_|
```

A Claude Code plugin for building AI-native products.

## Install

Add the marketplace once (this is global):

```
/plugin marketplace add Play-New/superskills
```

Then install in the project where you need it:

```
/plugin install super@superskills --scope project
```

This activates the plugin only in that project. Hooks, commands, and skills stay contained — other projects are not affected.

To update: `/plugin marketplace update superskills`

Run `/super:strategy` — it scans the folder, detects the stack, asks five questions (who's it for, who uses it, what they need, what's needed, what exists), then web research, EIID mapping, value chain, and strategic classification. It writes CLAUDE.md and `.superskills/`.

From that point: the secrets guard watches file writes, skills advise during planning, commands audit specific domains, and findings accumulate in `.superskills/`.

## Commands

| Command | Does |
|---------|------|
| `/super:strategy` | Assessment, EIID mapping, value chain, scaffolding, priorities — or alignment review and opportunity scan |
| `/super:design` | Design system with craft: intent-first init, information architecture, token enforcement, consistency audit |
| `/super:review` | Full quality audit: tests, security, strategy alignment, design consistency, performance |

Each command detects whether setup has been done. First run configures. After that, it audits.

## Hooks

One hook fires automatically:

```
  You write or edit a file
       │
       ▼
  ┌─────────────────┐
  │ Secrets guard    │  After file write/edit: blocks hardcoded
  │ (post-tool)      │  secrets in source code
  └─────────────────┘
```

Two skills fire during planning:
- **EIID awareness** reads CLAUDE.md for the EIID mapping and flags work that doesn't trace to any layer.
- **Design awareness** reads `.superskills/design-system.md` and flags component builds when registry alternatives exist, aesthetic drift from the established direction, new variants when existing patterns fit, and changes that violate the documented information architecture (navigation budget, focal points, content depth tiers).

Design token enforcement and test running are on-demand: `/super:design` audits tokens, `/super:review` runs the full suite. They don't fire automatically because Stop hooks add latency and noise to every session.

## What blocks

**Secrets guard:** hardcoded secrets in source code. Ignores .env, config, migrations, SQL, lock files, markdown, JSON config.

## Where things go

Two concerns, two places.

**CLAUDE.md** contains stable project instructions: context, stack, EIID mapping, strategic classification, technology constraints, design system config. It changes rarely. Claude reads it at session start to understand the project. Target: under 100 lines.

**`.superskills/`** contains volatile findings:
- `report.md` — security, design, performance, test findings. Replaced on each audit. Status counts at the top. Project Profile tracks recurring patterns across reviews.
- `decisions.md` — architecture decisions log. Append-only.
- `design-system.md` — design direction, tokens, component patterns. Updated as the system evolves.

Commands **read** CLAUDE.md for context and **write** to `.superskills/`. This follows the same pattern as Anthropic's code-review plugin (reads CLAUDE.md, writes findings elsewhere) and Trail of Bits skills (standalone report files).

---

## What AI-native means

Most software built with AI assistance is traditional software that happened to be written faster. The architecture stays the same: forms, CRUD, dashboards. AI helped type it, but the product could have been built in 2015.

AI-native products are structured differently. They collect data from multiple sources, detect patterns and anomalies in that data, turn raw analysis into statements a human can act on, and deliver those statements where people already work, triggered by conditions that matter. A traditional CRM shows you a dashboard. An AI-native CRM sends your sales rep a WhatsApp message at 9am saying "Acme Corp's order volume dropped 30% this month, here are three possible reasons" and lets them respond with a voice note.

The difference is architectural. You need to think about data flow, inference, interpretation, and delivery as separate layers from the start.

## EIID

SuperSkills organizes this into four layers.

```
 ┌─────────────────────────────────────────┐
 │  ENRICHMENT      sources + normalize     │
 ├─────────────────────────────────────────┤
 │  INFERENCE       detect, predict, flag   │
 ├─────────────────────────────────────────┤
 │  INTERPRETATION  insights + framing      │
 ├─────────────────────────────────────────┤
 │  DELIVERY        channels + triggers     │
 └─────────────────────────────────────────┘
```

**Enrichment** is where data enters the system. Not just your database: Gmail inboxes, ERP exports, spreadsheets, public APIs, partner data feeds. A hardware distributor might have order history in SAP, supplier catalogs in Excel, and customer complaints in a shared mailbox. Enrichment means connecting all three and normalizing them into something inference can work with.

**Inference** detects patterns, makes predictions, flags anomalies. This is the layer AI has commoditized fastest. What used to require a data science team and months of work is now an API call: "which orders deviate more than 20% from historical average?" or "which customers are likely to churn based on declining order frequency?" The compute is cheap. The hard part is knowing what questions to ask.

**Interpretation** turns raw inference into something a person can act on. "Customer #4521 anomaly score: 0.87" means nothing. "Acme Corp's orders dropped 30% vs. last quarter, likely due to their Q3 budget freeze, and they have a renewal in 45 days" is an insight. Interpretation adds context: comparison to baselines, trend direction, likely explanation, and a recommended action.

**Delivery** pushes insights where people already work. Email, Slack, WhatsApp, Telegram, SMS. Triggered by conditions: a threshold crossed, a schedule, an event, a user request. The critical design choice is timing. An insight about a churning customer is worth nothing if it arrives after the renewal date. A stock alert matters at market open, not at midnight.

Dashboards are configuration surfaces, not discovery tools. The product should go where the user already is.

## The value chain

`/super:strategy` scans the folder, detects the stack, and asks five questions: who's it for, who uses it, what they need, what's needed to deliver it, what exists already. Then it researches the problem space and deduces the rest — dependencies, evolution, strategic classification.

This produces a chain of dependencies from user need down to infrastructure. Every component in the system exists because something above it requires it. If you can't trace a component back to the user need, it shouldn't be there.

## Where value sits

Components evolve. What was novel becomes custom, then product, then commodity. A churn prediction model that took a data science team six months is now an API call. The compute is cheap. The hard part is knowing what questions to ask.

Three types of constraints determine where to focus:

**Commodity components** are well-understood, with multiple interchangeable providers. Building these in-house is waste. Use utilities, APIs, existing services. Authentication, storage, email delivery, basic analytics — don't build what you can buy.

**Judgment components** require human decision-making with real consequences. AI cannot own outcomes. A system can flag that a customer is likely to churn, but deciding whether to offer a 20% discount or call the CEO requires someone who understands the relationship and is willing to be wrong. These components need better information flowing to the human, not automation replacing them.

**Coordination components** connect systems, teams, or data sources in ways that were previously too expensive. A CRM, an ERP, and a supplier portal in a single inference pipeline used to require a year-long integration project. Now it's a configuration problem. New value emerges where these connections create insights nobody had before.

When `/super:strategy` maps a project, it classifies each component:

**Automate.** Commodity. Don't build what you can buy.

**Differentiate.** Judgment required. Enhance with better information.

**Innovate.** New coordination. Build it — this is where value is created.

## Review

`/super:review` runs all audits on the full project: tests, security, strategy alignment, design consistency, performance. Tests run first — auditing broken code wastes time. If tests fail, it asks whether to continue with the remaining audits or stop. If agent teams are available, the remaining four audits run in parallel. Otherwise, they run in order.

The review covers:
- **Tests:** vitest + Playwright setup (if missing) and full suite run
- **Security:** OWASP Top 10, GDPR (6 checks), secrets scan, stack-adaptive checks (Supabase RLS, Vercel env vars, Inngest signing keys, Next.js Server Actions)
- **Strategy:** EIID alignment per file, scope creep detection, 11-question opportunity scan
- **Design:** accessibility (WCAG 2.1 AA, 8 checks), information architecture (nav budget, focal points, content depth, screen coverage), cross-file consistency, craft advisory (name test, swap test, composition, atmosphere, motion)
- **Performance:** bundle analysis, Core Web Vitals, N+1 queries, API costs, stack-adaptive checks

## Design rules

The design skill adapts to the project's UI framework. It detects what's installed and applies the right rules.

**Information architecture** (init mode, before any visual decisions):
- Core objects: 3-6 things users care about, each mapping to a nav destination
- Navigation budget: 5-8 items for sidebar, 3-5 for top bar, everything else nests or goes to settings
- Screen map with one focal point per screen and 2-4 elements above the fold
- Content depth tiers: surface (daily), one click (weekly), deep (monthly) — every feature belongs to exactly one tier

**Universal rules** (always enforced, any framework):
- WCAG 2.1 AA: 4.5:1 contrast, focus states, alt text, form labels, 44x44px touch targets
- `cursor-pointer` on all clickable elements
- Responsive: works at 320px, consistent breakpoints

Token compliance (no hardcoded colors, fonts, arbitrary values in UI files) is enforced by the token guard hook, not the design review.

**Framework-specific rules** (adapted to detected stack):
- Component-library projects: search existing registries before building custom, use semantic tokens, follow the framework's conventions
- CSS-utility projects: utility classes only, no arbitrary values, no custom CSS classes
- Component frameworks: use the framework's API, theme overrides in the theme file

If no framework is detected, only universal rules apply.

## Stack

`/super:strategy` recommends a stack for new projects based on what the EIID mapping needs. For existing projects, it detects what's installed and adapts.

Five infrastructure roles to fill:

| Role | Criteria |
|------|----------|
| Database + auth | Managed, with row-level security and realtime |
| Hosting | Edge-capable, preview deployments |
| Workflows | Scheduled jobs, retry, event-driven |
| Frontend | Server-side rendering, streaming |
| UI components | Registry-based, token-driven styling |

Delivery channels (email, messaging, SMS) and enrichment tools (scraping, APIs) are added when the EIID mapping calls for them. The plugin doesn't prescribe specific tools. It detects what's installed and enforces consistency with that choice.

## Structure

```
superskills/                             the plugin
├── .claude-plugin/
│   ├── plugin.json                     plugin name: "super"
│   └── marketplace.json                marketplace definition
├── .githooks/
│   └── pre-commit                      auto-version on commit
├── commands/
│   ├── strategy.md                     entry point: assessment, EIID, priorities
│   ├── design.md                       design system with craft
│   └── review.md                       full quality audit
├── skills/
│   ├── eiid-awareness/SKILL.md          auto-invoked during planning
│   └── design-awareness/SKILL.md        auto-invoked during planning
├── agents/
│   └── stop-tests.md                    test runner agent
├── hooks/hooks.json                     secrets guard only
├── reference/
│   ├── claude-md-template.md            CLAUDE.md blank structure
│   ├── design-system-template.md        design-system.md blank structure
│   ├── decisions-template.md            decisions.md blank structure
│   ├── design-critique.md              4-layer critique framework
│   ├── design-craft.md                 subtle layering, motion, atmosphere, color
│   └── examples/
│       ├── claude-md-saas.md            filled example (fleet management SaaS)
│       ├── design-system-saas.md        filled example (Nova style, dense data)
│       └── decisions-saas.md            filled example (2 weeks of decisions)
└── README.md

your-project/                            what gets generated
├── CLAUDE.md                            stable instructions (~100 lines)
└── .superskills/
    ├── report.md                        volatile findings (replaced each audit)
    ├── decisions.md                     architecture log (append-only)
    └── design-system.md                 design direction + tokens + patterns
```

3 commands, 2 skills, 1 agent, 1 hook. 15 markdown files, 3 JSON in the plugin. Reference files show what the output looks like and how to execute craft.

## References

The strategic thinking behind SuperSkills draws from Simon Wardley's value chain mapping and evolution axis, and Sangeet Paul Choudary's work on platform dynamics and AI-driven value chain restructuring.

## License

MIT

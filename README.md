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

Run `/super:start` — five questions (who's it for, who uses it, what they need, what's needed to deliver it, what exists already), then folder scan, web research, and EIID mapping. The plugin deduces the rest: value chain, evolution, strategic classification. Or run `/super:scan` to skip the questions — it reads everything in the folder and tells you what the project could become.

From that point: hooks watch what you build, commands audit specific domains, and findings accumulate in `.superskills/`.

## Commands

| Command | Does |
|---------|------|
| `/super:start` | Full assessment, web research, EIID mapping, writes CLAUDE.md |
| `/super:scan` | Read everything, see it through EIID eyes, write what it could become |
| `/super:review` | Full audit: tests, trust, strategy, design, performance |
| `/super:strategy` | Alignment check against EIID, scope creep detection, opportunity scan |
| `/super:trust` | OWASP Top 10, GDPR, secrets scan, auth verification |
| `/super:design` | Design system with craft: intent-first init, token enforcement, consistency audit |
| `/super:test` | vitest + Playwright, blocks on failure |
| `/super:performance` | Bundle size, Core Web Vitals, N+1 queries, API costs |

Each command detects whether setup has been done. First run configures. After that, it audits.

## Hooks

Three checks fire on their own:

```
  You write code
       │
       ▼
  ┌─────────────────┐
  │ Secrets guard    │  After file write/edit: blocks hardcoded
  │ (post-tool)      │  secrets in source code
  └─────────────────┘
       │
       ▼
  Session ends
       │
       ▼
  ┌─────────────────┐
  │ Token guard      │  Before task completion: blocks hardcoded
  │ (stop)           │  colors, fonts, arbitrary values in UI files
  └─────────────────┘  (only if .superskills/design-system.md exists)
       │
       ▼
  ┌─────────────────┐
  │ Test gate        │  Before task completion: runs test suite.
  │ (stop)           │  Blocks if tests fail.
  └─────────────────┘
```

Two skills fire during planning:
- **EIID awareness** reads CLAUDE.md for the EIID mapping and flags work that doesn't trace to any layer.
- **Design awareness** reads `.superskills/design-system.md` and flags component builds when registry alternatives exist, aesthetic drift from the established direction, and new variants when existing patterns fit.

For a full audit (security, design, strategy, performance), run `/super:review` when you're ready.

## What blocks

**Secrets guard:** hardcoded secrets in source code. Ignores .env, config, migrations, SQL, lock files, markdown, JSON config.

**Token guard:** hardcoded color values, font-family declarations, arbitrary Tailwind values in UI component files. Runs once at session end instead of per-edit. Only active when `.superskills/design-system.md` exists. Ignores .css files, config files, theme files.

**Test gate:** failing tests. Skips if no tests exist.

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

`/super:start` asks five questions: who's it for, who uses it, what they need, what's needed to deliver it, what exists already. Then it researches the problem space and deduces the rest — dependencies, evolution, strategic classification.

This produces a chain of dependencies from user need down to infrastructure. Every component in the system exists because something above it requires it. If you can't trace a component back to the user need, it shouldn't be there.

## Where value sits

Components evolve. What was novel becomes custom, then product, then commodity. A churn prediction model that took a data science team six months is now an API call. The compute is cheap. The hard part is knowing what questions to ask.

Three types of constraints determine where to focus:

**Commodity components** are well-understood, with multiple interchangeable providers. Building these in-house is waste. Use utilities, APIs, existing services. Authentication, storage, email delivery, basic analytics — don't build what you can buy.

**Judgment components** require human decision-making with real consequences. AI cannot own outcomes. A system can flag that a customer is likely to churn, but deciding whether to offer a 20% discount or call the CEO requires someone who understands the relationship and is willing to be wrong. These components need better information flowing to the human, not automation replacing them.

**Coordination components** connect systems, teams, or data sources in ways that were previously too expensive. A CRM, an ERP, and a supplier portal in a single inference pipeline used to require a year-long integration project. Now it's a configuration problem. New value emerges where these connections create insights nobody had before.

When `/super:start` maps a project, it classifies each component:

**Automate.** Commodity. Don't build what you can buy.

**Differentiate.** Judgment required. Enhance with better information.

**Innovate.** New coordination. Build it — this is where value is created.

## Review

`/super:review` runs all audits on the full project: tests, trust, strategy, design, performance. Tests run first — auditing broken code wastes time. If agent teams are available, the remaining four audits run in parallel. Otherwise, they run in order.

## Design rules

The design skill adapts to the project's UI framework. It detects what's installed and applies the right rules.

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

`/super:start` recommends a stack for new projects based on what the EIID mapping needs. For existing projects, it detects what's installed and adapts.

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
│   ├── start.md                         full assessment entry point
│   ├── scan.md                          EIID analysis (no questions)
│   ├── review.md                        parallel audit (agent teams)
│   ├── strategy.md
│   ├── trust.md
│   ├── design.md
│   ├── test.md
│   └── performance.md
├── skills/
│   ├── eiid-awareness/SKILL.md          auto-invoked during planning
│   └── design-awareness/SKILL.md        auto-invoked during planning
├── agents/
│   └── stop-tests.md                    test gate
├── hooks/hooks.json
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

20 markdown files, 3 JSON in the plugin. Commands under 3K tokens each. Reference files show what the output looks like and how to execute craft.

## References

The strategic thinking behind SuperSkills draws from Simon Wardley's value chain mapping and evolution axis, and Sangeet Paul Choudary's work on platform dynamics and AI-driven value chain restructuring.

## License

MIT

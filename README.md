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
| `/super:design` | Framework-adaptive design audit, WCAG 2.1 AA, design tokens |
| `/super:test` | vitest + Playwright, blocks on failure |
| `/super:performance` | Bundle size, Core Web Vitals, N+1 queries, API costs |

Each command detects whether setup has been done. First run configures. After that, it audits.

## Hooks

Two checks fire on their own:

```
  You write code
       │
       ▼
  ┌────────────┐
  │ Write guard │  After file write/edit: blocks hardcoded
  │ (post-tool) │  secrets in source code
  └────────────┘
       │
       ▼
  ┌────────────┐
  │ Test gate   │  Before task completion: runs test suite.
  │ (stop)      │  Blocks if tests fail.
  └────────────┘
```

An EIID awareness skill also fires during planning. It reads CLAUDE.md for the EIID mapping and flags work that doesn't trace to any layer.

For a full audit (security, design, strategy, performance), run `/super:review` when you're ready.

## What blocks

**Write guard:** hardcoded secrets in source code. Ignores .env, config, migrations, SQL, lock files, markdown, JSON config.

**Test gate:** failing tests. Skips if no tests exist.

## Where things go

Two concerns, two places.

**CLAUDE.md** contains stable project instructions: context, stack, EIID mapping, strategic classification, technology constraints, design system config. It changes rarely. Claude reads it at session start to understand the project. Target: under 100 lines.

**`.superskills/`** contains volatile findings:
- `report.md` — security, design, performance, test findings. Replaced on each audit. Status counts at the top. Project Profile tracks recurring patterns across reviews.
- `decisions.md` — architecture decisions log. Append-only.

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

The design skill adapts to the project's UI framework. It detects what's installed (shadcn, Chakra, MUI, Mantine, Tailwind, or nothing) and applies the right rules.

**Universal rules** (always enforced, any framework):
- WCAG 2.1 AA: 4.5:1 contrast, focus states, alt text, form labels, 44x44px touch targets
- Token compliance: no hardcoded color values in components
- `cursor-pointer` on all clickable elements
- No Unicode escape sequences for accents (UTF-8 directly)
- Responsive: works at 320px, consistent breakpoints

**Framework-specific rules** (only for detected framework):
- shadcn + Tailwind: check shadcnblocks registry via CLI (`npx shadcn add @shadcnblocks/<block>`) before building custom sections, shadcn base components second, no custom CSS, no arbitrary Tailwind values
- Chakra/MUI/Mantine: use framework components before custom, style through framework APIs
- Tailwind only: no custom CSS classes, no arbitrary values

If no framework is detected, only universal rules apply.

## Default stack

`/super:start` recommends this for new projects. Audits adapt to whatever is installed.

| Default | Role |
|---------|------|
| Supabase | Database, auth, storage, embeddings |
| Vercel | Hosting, edge functions |
| Inngest | Workflows, cron, retry |
| Next.js | Frontend, Server Components default |
| shadcn + Tailwind | UI components and styling (suggested, not imposed) |
| shadcnblocks registry | Pre-built sections via CLI: `npx shadcn add @shadcnblocks/<block>` |

| When needed | Role |
|-------------|------|
| Brevo | Email/SMS/WhatsApp delivery |
| Telegram/Slack/Discord SDK | Messaging delivery |
| Apify/Supermemory/Playwright | Enrichment, scraping |

## Structure

```
superskills/                             the plugin
├── .claude-plugin/
│   ├── plugin.json                     plugin name: "super"
│   └── marketplace.json                marketplace definition
├── commands/
│   ├── start.md                         full assessment entry point
│   ├── scan.md                          EIID analysis (no questions)
│   ├── review.md                        parallel audit (agent teams)
│   ├── strategy.md
│   ├── trust.md
│   ├── design.md
│   ├── test.md
│   └── performance.md
├── skills/eiid-awareness/SKILL.md       auto-invoked during planning
├── agents/
│   └── stop-tests.md                    test gate
├── hooks/hooks.json
└── README.md

your-project/                            what gets generated
├── CLAUDE.md                            stable instructions (~100 lines)
└── .superskills/
    ├── report.md                        volatile findings (replaced each audit)
    └── decisions.md                     architecture log (append-only)
```

11 markdown files, 3 JSON in the plugin. Each command under 3K tokens. Fits in one context window.

## References

The strategic thinking behind SuperSkills draws from Simon Wardley's value chain mapping and evolution axis, and Sangeet Paul Choudary's work on platform dynamics and AI-driven value chain restructuring.

## License

MIT

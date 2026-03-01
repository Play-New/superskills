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

## Update

Manual update:

```
/plugin marketplace update superskills
```

To receive updates automatically at startup, enable auto-update on the marketplace:

1. Run `/plugin`
2. Go to the **Marketplaces** tab
3. Select `superskills`
4. Select **Enable auto-update**

From that point, Claude Code checks for new versions when it starts. If the plugin was updated, it shows a notification to restart.

### Team setup

Add the marketplace and pre-enable the plugin in `.claude/settings.json` so new members get it automatically when they trust the project folder:

```json
{
  "extraKnownMarketplaces": {
    "superskills": {
      "source": {
        "source": "github",
        "repo": "Play-New/superskills"
      }
    }
  },
  "enabledPlugins": {
    "super@superskills": true
  }
}
```

Run `/super:strategy` — it scans the folder, detects the stack, asks six questions (who's it for, who uses it, how they work today, what they need, what's needed, what exists), then web research, EIID mapping, value chain, and strategic classification. It writes CLAUDE.md and `.superskills/`.

From that point: the secrets guard watches file writes, skills advise during planning (and nudge when files are stale), commands audit specific domains, and findings accumulate in `.superskills/`.

## Commands

| Command | Does |
|---------|------|
| `/super:strategy` | Assessment, EIID mapping, value chain, scaffolding, priorities — or alignment review, opportunity scan, and CLAUDE.md refresh |
| `/super:design` | Three modes: init (direction assessment with references, IA, tokens), redesign (single-screen craft improvement with a target), review (consistency audit) |
| `/super:review` | Full quality audit: tests, security, strategy alignment, design consistency, performance |

Each command detects whether setup has been done. First run configures. After that, it audits. `/super:design` with a specific target (file path, screenshot, URL) triggers redesign mode.

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
- **EIID awareness** reads CLAUDE.md for the EIID mapping and flags work that doesn't trace to any layer. Nudges toward `/super:strategy` when CLAUDE.md is stale (untracked deps, unmapped files, shifted approaches).
- **Design awareness** reads `.superskills/design-system.md` and flags component builds when registry alternatives exist, aesthetic drift from the established direction, new variants when existing patterns fit, and changes that violate the documented information architecture. Nudges toward `/super:design` when the component patterns table falls behind the codebase.

Design token enforcement and test running are on-demand: `/super:design` audits tokens, `/super:review` runs the full suite. They don't fire automatically because Stop hooks add latency and noise to every session.

## What blocks

**Secrets guard:** hardcoded secrets in source code. Ignores .env, config, migrations, SQL, lock files, markdown, JSON config.

## Where things go

Two concerns, two places.

**CLAUDE.md** contains stable project instructions: context, stack, EIID mapping with strategic approach per layer, technology constraints, design system config. It changes rarely — updated by `/super:strategy` (init or review refresh) and `/super:design` (Design System section). Claude reads it at session start to understand the project. Target: under 100 lines.

**`.superskills/`** contains volatile findings:
- `report.md` — security, design, performance, test findings. Replaced on each audit. Status counts at the top. Project Profile tracks recurring patterns across reviews.
- `decisions.md` — architecture decisions log. Append-only. Updated by `/super:strategy` review, `/super:review`, and `/super:design` redesign mode.
- `design-system.md` — design direction, references, tokens, component patterns. Updated by `/super:design` as the system evolves.

Commands **read** CLAUDE.md for context and **write** to `.superskills/`. This follows the same pattern as Anthropic's code-review plugin (reads CLAUDE.md, writes findings elsewhere) and Trail of Bits skills (standalone report files).

---

## What AI-native means

Most software built with AI assistance is traditional software that happened to be written faster. The architecture stays the same: forms, CRUD, dashboards. AI helped type it, but the product could have been built in 2015.

AI-native products are structured differently. The intelligence acts as an invisible layer that wraps around existing behavior. People feed data the way they already work: a photo of a handwritten note, a forwarded email, a voice message on WhatsApp, a Slack thread. They don't learn a new interface or change their habits. The system collects from all these entry points, normalizes the data, detects patterns, turns analysis into actionable statements, and delivers results back through the same channels people chose. A traditional CRM shows you a dashboard. An AI-native CRM accepts a voice note from your sales rep on WhatsApp and sends them back "Acme Corp's order volume dropped 30% this month, here are three possible reasons" at 9am the next day.

The difference is architectural. You need to think about data collection, inference, interpretation, and delivery as separate layers from the start. And you need to accept that input and output often share the same channels.

## EIID

SuperSkills organizes this into four layers.

```
 ┌─────────────────────────────────────────┐
 │  ENRICHMENT      collect + normalize      │
 ├─────────────────────────────────────────┤
 │  INFERENCE       detect, predict, flag   │
 ├─────────────────────────────────────────┤
 │  INTERPRETATION  insights + framing      │
 ├─────────────────────────────────────────┤
 │  DELIVERY        channels + triggers     │
 └─────────────────────────────────────────┘
```

**Enrichment** is where data enters the system. Not just databases and APIs: a photo of a handwritten inventory sheet, a forwarded email from a supplier, a voice note on WhatsApp, a Slack message, an ERP export, a spreadsheet attachment. People should not have to change how they work. A hardware distributor might have order history in SAP, supplier catalogs in Excel, customer complaints in a shared mailbox, and a warehouse manager who texts stock counts from his phone. Enrichment means accepting all of these, normalizing them into something inference can work with.

**Inference** detects patterns, makes predictions, flags anomalies. This is the layer AI has commoditized fastest. What used to require a data science team and months of work is now an API call: "which orders deviate more than 20% from historical average?" or "which customers are likely to churn based on declining order frequency?" The compute is cheap. The hard part is knowing what questions to ask.

**Interpretation** turns raw inference into something a person can act on. "Customer #4521 anomaly score: 0.87" means nothing. "Acme Corp's orders dropped 30% vs. last quarter, likely due to their Q3 budget freeze, and they have a renewal in 45 days" is an insight. Interpretation adds context: comparison to baselines, trend direction, likely explanation, and a recommended action.

**Delivery** returns results through the same channels people already use. The person who sent a voice note on WhatsApp gets the answer on WhatsApp. The one who forwarded an email gets a reply in their inbox. Input and output share channels. Delivery is triggered by conditions: a threshold crossed, a schedule, an event, a user request. The critical design choice is timing. An insight about a churning customer is worth nothing if it arrives after the renewal date. A stock alert matters at market open, not at midnight.

The web interface serves two roles: visualizations that don't fit in a message (charts, maps, timelines, complex comparisons) and configuration. Configuration is the control plane for the invisible layer: enrichment sources and polling frequency, inference prompts and thresholds, interpretation framing, delivery channels per user and trigger rules, user management and cron schedules. It is not the primary input surface. If users need to type data into a dashboard to get value from the product, the enrichment layer has a gap.

## The value chain

`/super:strategy` scans the folder, detects the stack, and asks six questions: who's it for, who uses it, how they work today, what they need, what's needed to deliver it, what exists already. Then it researches the problem space and deduces the rest — dependencies, evolution, strategic classification.

This produces a chain of dependencies from user need down to infrastructure. Every component in the system exists because something above it requires it. If you can't trace a component back to the user need, it shouldn't be there.

## Where value sits

Components evolve. What was novel becomes custom, then product, then commodity. A churn prediction model that took a data science team six months is now an API call. The compute is cheap. The hard part is knowing what questions to ask.

Each EIID layer carries a strategic approach:

**Automate.** Commodity components with multiple interchangeable providers. Authentication, storage, email delivery, basic analytics. Don't build what you can buy.

**Differentiate.** Components requiring human judgment with real consequences. A system can flag that a customer is likely to churn, but deciding whether to offer a 20% discount or call the CEO requires someone who understands the relationship. These components need better information flowing to the human, not automation replacing them.

**Innovate.** New connections between systems, teams, or data sources that were previously too expensive. A CRM, an ERP, and a supplier portal in a single inference pipeline used to require a year-long integration project. Now it's a configuration problem. New value emerges where these connections create insights nobody had before.

The approach lives inside each EIID layer, not in a separate section, because the same layer can contain components at different evolution stages. A fleet app's enrichment layer might automate telematics polling (commodity) while differentiating OBD-II integration (hardware-dependent, needs fleet manager input).

## Design

`/super:design` has three modes:

**Init mode** explores the product's world, collects references (URLs, screenshots, Figma files, brand assets), defines information architecture grounded in the EIID mapping, and generates a token-based design system.

**Redesign mode** activates when a specific target is provided (file path, screenshot, URL). It loads whatever design context exists (SuperSkills-managed or extracted from code), runs a strategic critique (six layers: strategic alignment, composition, craft, content, structure, identity), collects screen-specific references, proposes changes, and implements approved changes. Works on any project with a design system, not just SuperSkills projects.

**Review mode** audits consistency, accessibility, and craft across the whole codebase.

The design critique starts from strategy. The interface is not the primary input surface: people feed data through their existing channels (chat, email, voice, photos). The interface shows visualizations that don't fit in a message and configuration that controls the system. Interpretation outputs (charts, trends, comparisons) get the most screen space. Enrichment and delivery configuration stays buried. A beautiful interface that misaligns with the value chain is a failure.

**Information architecture** (init mode, before any visual decisions):
- Core objects: 3-6 things users care about, each mapping to a nav destination
- Navigation budget: 5-8 items for sidebar, 3-5 for top bar, everything else nests or goes to settings
- Screen map with one focal point per screen and 2-4 elements above the fold
- Content depth tiers: surface (daily), one click (weekly), deep (monthly) — every feature belongs to exactly one tier

**Direction assessment** (init mode, after product exploration):
- References: URLs, screenshots, Figma files, brand names, designers, design studios — analyzed for applicable patterns
- Anti-references: things to explicitly avoid
- Existing assets: brand materials that constrain choices
- User confirmation of the proposed direction

**Universal rules** (always enforced, any framework):
- WCAG 2.1 AA: 4.5:1 contrast, focus states, alt text, form labels, 44x44px touch targets
- `cursor-pointer` on all clickable elements
- Responsive: works at 320px, consistent breakpoints

**Framework-specific rules** (adapted to detected stack):
- Component-library projects: search existing registries before building custom, use semantic tokens, follow the framework's conventions
- CSS-utility projects: utility classes only, no arbitrary values, no custom CSS classes
- Component frameworks: use the framework's API, theme overrides in the theme file

If no framework is detected, only universal rules apply.

## Review

`/super:review` runs all audits on the full project: tests, security, strategy alignment, design consistency, performance. Tests run first — auditing broken code wastes time. If tests fail, it asks whether to continue with the remaining audits or stop. If agent teams are available, the remaining four audits run in parallel. Otherwise, they run in order.

The review covers:
- **Tests:** vitest + Playwright setup (if missing) and full suite run
- **Security:** OWASP Top 10, GDPR (6 checks), secrets scan, stack-adaptive checks (Supabase RLS, Vercel env vars, Inngest signing keys, Next.js Server Actions)
- **Strategy:** EIID alignment per file, scope creep detection, 11-question opportunity scan
- **Design:** accessibility (WCAG 2.1 AA, 8 checks), information architecture review, cross-file consistency, craft advisory (six-layer critique)
- **Performance:** bundle analysis, Core Web Vitals, N+1 queries, API costs, stack-adaptive checks

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
│   ├── design.md                       design system: init, redesign, review
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
│   ├── design-critique.md              6-layer critique (strategic alignment through identity)
│   ├── design-craft.md                 direction, spatial composition, typography, identity, layering, atmosphere, motion, color
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
    └── design-system.md                 design direction + references + tokens + patterns
```

3 commands, 2 skills, 1 agent, 1 hook. 15 markdown files, 3 JSON in the plugin. Reference files show what the output looks like and how to execute craft.

## References

The strategic thinking behind SuperSkills draws from Simon Wardley's value chain mapping and evolution axis, and Sangeet Paul Choudary's work on platform dynamics and AI-driven value chain restructuring.

## License

MIT

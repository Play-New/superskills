```
 ____                       ____  _    _ _ _
/ ___| _   _ _ __   ___ _ _/ ___|| | _(_) | |___
\___ \| | | | '_ \ / _ \ '__\___ \| |/ / | | / __|
 ___) | |_| | |_) |  __/ |  ___) |   <| | | \__ \
|____/ \__,_| .__/ \___|_| |____/|_|\_\_|_|_|___/
             |_|
```

A Claude Code plugin that changes the shape of software.

```bash
claude plugin marketplace add Play-New/superskills
```

```bash
claude plugin install super
```

---

## The problem

Most software built with AI assistance is traditional software that happened to be written faster. The architecture stays the same: forms, CRUD, dashboards. AI helped type it, but the product could have been built in 2015.

This is a waste of the most important shift in software history. The opportunity is not faster typing. It is a different kind of product — one where intelligence is structural, not decorative.

## What makes this different

SuperSkills is not a scaffolder, a boilerplate generator, or a prompt library. It is a framework for building **AI-native products**: services, applications, and tools where the intelligence is an invisible layer that wraps around existing behavior.

The difference is architectural. In a traditional CRM, you open a dashboard and type data into forms. In an AI-native CRM, a sales rep sends a voice note on WhatsApp and gets back "Acme Corp's order volume dropped 30% this month, here are three possible reasons" at 9am the next day. No new interface to learn. The system meets people where they already work.

SuperSkills enforces this architecture through EIID — a four-layer model that restructures every product around how intelligence flows from raw data to user value. Every component, every screen, every agent, every line of code traces back to this structure. If it doesn't trace, it doesn't get built.

No other tool does this. Existing frameworks help you build faster. SuperSkills changes **what** you build.

---

## EIID

Four layers. Every AI-native product maps onto them.

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

**Enrichment** is where data enters the system. Not just databases and APIs: a photo of a handwritten inventory sheet, a forwarded email from a supplier, a voice note on WhatsApp, a Slack message, an ERP export, a spreadsheet attachment. People should not have to change how they work. Enrichment means accepting data from every channel they already use, normalizing it into something inference can work with.

**Inference** detects patterns, makes predictions, flags anomalies. What used to require a data science team and months of work is now an API call: "which orders deviate more than 20% from historical average?" The compute is cheap. The hard part is knowing what questions to ask.

**Interpretation** turns raw inference into something a person can act on. "Customer #4521 anomaly score: 0.87" means nothing. "Acme Corp's orders dropped 30% vs. last quarter, likely due to their Q3 budget freeze, and they have a renewal in 45 days" is an insight. Interpretation adds context, comparison, explanation, and a recommended action.

**Delivery** returns results through the same channels people already use. The person who sent a voice note on WhatsApp gets the answer on WhatsApp. The one who forwarded an email gets a reply in their inbox. Delivery is triggered by conditions: a threshold crossed, a schedule, an event, a user request. The critical design choice is timing — an insight about a churning customer is worth nothing if it arrives after the renewal date.

The web interface, when one exists, serves two roles: visualizations that don't fit in a message (charts, maps, timelines) and configuration of the invisible layer. It is not the primary input surface. If users need to type data into a dashboard to get value, the enrichment layer has a gap.

### How EIID changes decisions

Every component carries two classifications:

**Strategic classification** — where does it sit on the evolution axis?
- **Automate**: commodity, multiple interchangeable providers. Don't build what you can buy.
- **Differentiate**: requires human judgment with real consequences. Better information to the human, not automation replacing them.
- **Innovate**: new connections between systems that were previously too expensive to link. New value emerges from these connections.

**Implementation level** — how to build it?

| Level | When | Cost |
|-------|------|------|
| **LLM call** | Single prompt-response. Classification, extraction, summarization. | Lowest |
| **Workflow** | Fixed sequence of steps, some using LLM calls. | Low |
| **Agent** | Autonomous loop with tool use. Path emerges from context. | Medium |
| **Code** | Deterministic, high volume, low latency, auditable. | Free at scale |
| **Buy** | Commodity service. | Variable |

These are not permanent. The **graduation pattern** connects them: start from the simplest level that works. Graduate up when edge cases justify complexity. Graduate down when patterns stabilize and volume demands it. Each component records its graduation trigger: `Innovate via workflow. Graduate to code when recipe DB > 10k.`

### Agent architecture

When EIID components use agent, workflow, or LLM call, strategy adds an agent architecture step: orchestration pattern, tool design (one atomic thing per tool), UI-agent parity (every user action available as a tool), cost model (tokens × invocations = monthly cost), and state persistence (external, never conversation history).

---

## How it works

Four commands, three advisory skills, two hooks. The commands run the lifecycle. The skills watch while you work. The hooks guard boundaries.

### Commands

| Command | What it does |
|---------|------|
| `/super:strategy` | Scans the folder, asks seven questions, researches the problem space, produces the EIID mapping with strategic classification and implementation levels. If any component needs an agent, adds agent architecture. Outputs CLAUDE.md and `.superskills/`. With context about what changed: refresh mode. |
| `/super:design` | For each EIID layer: determines if it needs a visual surface, conversational interface, notification channel, or none. Defines experience patterns across all modalities. For visual layers: direction, IA, typography, tokens, component patterns. With a target: redesign mode. |
| `/super:build` | Reads the EIID mapping, checks readiness, decomposes the project into buildable pieces, constructs each with a test-first loop. Fully autonomous after plan approval. With a target: extend mode. |
| `/super:review` | Eight-domain audit: tests (blocking), security, build quality, strategy alignment, experience, design, performance, agent architecture. |

Each command detects whether setup exists and routes accordingly. Strategy must exist before design. Design must exist before build. Review can run anytime.

### Skills (advisory, fire automatically)

**EIID awareness** — during planning, flags work that doesn't trace to an EIID layer. Catches implementation level mismatches, parity gaps, stale mappings.

**Design awareness** — during planning, challenges whether changes earn their place. Checks experience alignment, registry alternatives, pattern consistency.

**Build awareness** — during implementation, challenges every addition against rule zero. Flags code that doesn't trace to EIID, simpler alternatives, generic output, buried prompts.

### Hooks

**Secrets guard** (PostToolUse) — blocks hardcoded secrets on file write/edit. Ignores .env, config, lock files, markdown.

**Session integrity** (SessionStart) — warns when CLAUDE.md has an EIID mapping but `.superskills/` is missing.

### Rule zero

Before adding anything — a screen, a component, an endpoint, a table — the system asks: does this trace to the EIID mapping? Is there a simpler way? Does it earn its place? A product with 3 screens that each do one thing perfectly beats a product with 12.

---

## What gets generated

**In your project:**

```
your-project/
├── CLAUDE.md                stable instructions (~100 lines)
└── .superskills/
    ├── report.md            volatile findings (replaced each audit)
    ├── decisions.md         architecture log (append-only)
    ├── design-system.md     direction + tokens + patterns
    └── build-plan.md        approved build plan (replaced each build)
```

**CLAUDE.md** contains the EIID mapping with strategic approach and implementation level per layer, technology constraints, design system config. Claude reads it at session start. It changes rarely.

**`.superskills/`** contains volatile findings that accumulate across sessions. Commands read CLAUDE.md for context and write to `.superskills/`. The build plan is approved once, then the build runs autonomously from it.

---

## Three example products

The `reference/examples/` directory contains complete outputs for three products that demonstrate the spectrum:

**FleetPulse** (SaaS) — vehicle fleet management. Visual-heavy, dense operational data. Every component is code or buy. No agents, no LLM calls. Demonstrates that AI-native architecture applies even when the intelligence surface is minimal: the structure still follows EIID, the strategic classification still drives decisions.

**RecipeBox** (Consumer) — recipe management from pantry photos. Mixed modality: WhatsApp for input and delivery, web archive for visualization, agent for conversation. Demonstrates graduation triggers: the recipe parser starts as a workflow, graduates to code when the recipe database exceeds 10k entries.

**DepWatch** (DevTool) — dependency update risk analysis. No visual UI at all. CLI only. A single LLM call for interpretation, with a graduation trigger to template when patterns stabilize. Demonstrates that EIID applies to non-visual products: enrichment from package registries, inference via semver analysis, interpretation via LLM, delivery via terminal output.

Each example includes: CLAUDE.md, design-system.md, decisions.md, and build-plan.md.

---

## Stack

`/super:strategy` recommends a stack for new projects based on the EIID mapping. For existing projects, it detects what's installed and adapts.

| Role | Default | Why |
|------|---------|-----|
| Database + auth | Supabase | Managed, RLS, realtime |
| Hosting | Vercel | Edge, preview deployments |
| Workflows | Inngest | Scheduled jobs, retry, events |
| Frontend | Next.js | SSR, streaming |
| UI components | shadcn + Tailwind | Registry-based, token-driven |

Delivery channels and enrichment tools are added when the EIID mapping calls for them. Defaults are suggestions — the plugin adapts to any stack.

---

## Structure

```
superskills/                             the plugin
├── .claude-plugin/
│   └── plugin.json                     plugin definition
├── commands/
│   ├── strategy.md                     EIID mapping, classification, implementation levels
│   ├── design.md                       modality assessment, experience, visual execution
│   ├── build.md                        autonomous construction with quality gates
│   └── review.md                       eight-domain audit
├── skills/
│   ├── eiid-awareness/SKILL.md          strategic alignment (advisory, during planning)
│   ├── design-awareness/SKILL.md        experience alignment (advisory, during planning)
│   └── build-awareness/SKILL.md         construction quality (advisory, during implementation)
├── agents/
│   └── stop-tests.md                    test runner (on-demand)
├── hooks/hooks.json                     secrets guard + session integrity
├── reference/
│   ├── claude-md-template.md            CLAUDE.md structure
│   ├── design-system-template.md        design-system.md structure
│   ├── decisions-template.md            decisions.md structure
│   ├── report-template.md              report.md structure
│   ├── design-critique.md              6-layer strategic critique
│   ├── design-craft.md                 craft dimensions (experience + visual)
│   ├── design-init-guide.md            modality assessment through tokens
│   ├── build-principles.md             construction principles
│   ├── review-security-guide.md        security checklist
│   ├── review-performance-guide.md     performance checklist
│   └── examples/                       FleetPulse, RecipeBox, DepWatch
└── README.md
```

4 commands, 3 skills, 1 agent, 2 hooks. 31 markdown files, 2 JSON.

## References

Strategic thinking draws from Wardley's value chain mapping and evolution axis, Choudary's platform dynamics and AI-driven value restructuring, Steinberger's intelligence-where-the-user-works principle, and the SaaSpocalypse pattern (commodity layers collapse, value moves to orchestration and delivery).

## License

MIT

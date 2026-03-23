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

```
/plugin marketplace add Play-New/superskills
/plugin install super@superskills
```

Or interactively:

```
/plugin marketplace add Play-New/superskills
/plugin
```

Go to the **Discover** tab, select `super`, choose a scope (user, project, or local).

## Update

```
/plugin marketplace update superskills
```

To receive updates automatically:

1. Run `/plugin`
2. Go to the **Marketplaces** tab
3. Select `superskills`
4. Select **Enable auto-update**

### Team setup

Add the marketplace and pre-enable the plugin in `.claude/settings.json` so new members get it automatically when they trust the project folder:

```json
{
  "enabledPlugins": {
    "super@superskills": true
  },
  "extraKnownMarketplaces": {
    "superskills": {
      "source": {
        "source": "github",
        "repo": "Play-New/superskills"
      }
    }
  }
}
```

---

## What it does

Run `/super:strategy`. It scans the folder, detects the stack, asks six questions, researches the problem space, and produces an EIID mapping with strategic classification and implementation levels. If any component uses agent, workflow, or LLM call, it adds an agent architecture step. Output: CLAUDE.md (stable instructions) and `.superskills/` (volatile findings).

From that point: the secrets guard watches file writes, two skills advise during planning, four commands handle specific domains, and findings accumulate in `.superskills/`.

## Commands

| Command | Does |
|---------|------|
| `/super:strategy` | Assessment, EIID mapping, implementation levels, agent architecture, scaffolding. Refresh mode when context changes |
| `/super:design` | Init: modality assessment, direction, IA, tokens, interaction patterns. Redesign: single-screen craft improvement |
| `/super:build` | Autonomous construction from strategy and design. Readiness check, decomposition, build loop with quality gates. Extend mode for adding features |
| `/super:review` | Full audit: tests, security, strategy, design, performance, agent architecture |

Each command detects whether setup exists. `/super:strategy` with context about what changed triggers refresh mode. `/super:design` with a target (file path, screenshot, URL) triggers redesign mode. `/super:build` with a target (feature, EIID layer, component) triggers extend mode.

---

## AI-native architecture

Most software built with AI assistance is traditional software that happened to be written faster. The architecture stays the same: forms, CRUD, dashboards. AI helped type it, but the product could have been built in 2015.

AI-native products are structured differently. The intelligence is an invisible layer that wraps around existing behavior. People feed data the way they already work: a photo of a handwritten note, a forwarded email, a voice message on WhatsApp, a Slack thread. They don't learn a new interface. The system collects from these entry points, normalizes the data, detects patterns, turns analysis into actionable statements, and delivers results back through the same channels.

A traditional CRM shows a dashboard. An AI-native CRM accepts a voice note from a sales rep on WhatsApp and sends back "Acme Corp's order volume dropped 30% this month, here are three possible reasons" at 9am the next day.

The difference is architectural. Data collection, inference, interpretation, and delivery are separate layers from the start. Input and output share the same channels. An agent, a workflow, or a single LLM call can sit at any point in the chain, alongside traditional code. The question for each component is not "should we use AI" but "what is the right implementation level for this behavior."

## EIID

SuperSkills organizes this architecture into four layers.

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

`/super:strategy` produces a chain of dependencies from user need down to infrastructure. Every component in the system exists because something above it requires it. If you can't trace a component back to the user need, it shouldn't be there.

Components evolve. What was novel becomes custom, then product, then commodity. A churn prediction model that took a data science team six months is now an API call. Each EIID component carries two classifications: strategic and operational.

### Strategic classification

Where does this component sit on the evolution axis?

**Automate.** Commodity components with multiple interchangeable providers. Authentication, storage, email delivery, basic analytics. Don't build what you can buy. AI has accelerated this: what was custom six months ago may be commodity today.

**Differentiate.** Components requiring human judgment with real consequences. A system can flag that a customer is likely to churn, but deciding whether to offer a 20% discount or call the CEO requires someone who understands the relationship. These components need better information flowing to the human, not automation replacing them.

**Innovate.** New connections between systems, teams, or data sources that were previously too expensive to link. A CRM, an ERP, and a supplier portal in a single inference pipeline used to require a year-long integration project. Now it's a configuration problem. New value emerges where these connections create insights nobody had before.

The classification lives inside each EIID layer, not in a separate section, because the same layer can contain components at different evolution stages. A fleet app's enrichment layer might automate telematics polling (commodity) while differentiating OBD-II integration (hardware-dependent, needs fleet manager input on which vehicles).

### Implementation level

Strategic classification determines what to build. Implementation level determines how to build it.

| Level | When | Cost |
|-------|------|------|
| **LLM call** | Single prompt-response. No iteration, no tools. Classification, extraction, summarization. | Lowest |
| **Workflow** | Fixed sequence of steps, some using LLM calls. Orchestration in code, intelligence in nodes. | Low |
| **Agent** | Autonomous loop with tool use. Path emerges from context. Memory, scheduling, channels. | Medium |
| **Code** | Deterministic, high volume, low latency, auditable. | Free at scale |
| **Buy** | Commodity service. | Variable |

These are not permanent assignments. The **graduation pattern** connects them: start from the simplest level that works. An LLM call that covers 90% of cases beats an agent that covers 100% at 10x the cost. Graduate up (LLM call → workflow → agent) when edge cases justify the complexity. Graduate down (agent → workflow → code) when patterns stabilize and volume demands it.

Each EIID layer's Approach field records both dimensions: `Innovate via workflow. Graduate to code when recipe DB > 10k.` The graduation trigger documents when the decision should be revisited. `/super:review` checks graduation readiness: is volume approaching the threshold, have patterns stabilized, are edge cases still the driver of value.

The three example projects demonstrate the spectrum: FleetPulse uses code and buy exclusively (visual-heavy SaaS, high volume, deterministic). RecipeBox mixes agents and workflows with graduation triggers (conversational consumer product, flexibility is the feature). DepWatch uses a single LLM call for interpretation with a graduation trigger to template (CLI devtool, minimal intelligence surface).

### Agent architecture

Step 5b in `/super:strategy`. Runs only when EIID components use agent, workflow, or LLM call.

- **Orchestration:** single agent with tools, step pipeline, or router that dispatches to specialized agents. Start with the simplest. A single agent with 5 tools beats a multi-agent system for most products.
- **Tool design:** each tool does one atomic thing. "look up product", "check inventory", "create order" compose into process order. "process order" is too coarse for an agent to use flexibly.
- **Parity:** every action a user can take via UI should also be available as a tool for agents. List the gaps. Missing parity means agents cannot do what users can.
- **Cost model:** tokens per invocation × invocations per day = monthly cost. Compare with the cost of coding the same behavior. If the agent costs more than coding, graduate to code.
- **State persistence:** where does agent state live? Database, filesystem, git. Never conversation history as the primary store. Progress and decisions persist externally so any session can resume.

Code Architecture in CLAUDE.md gains five EIID-gated principles: parity, tool granularity, tests as reward signal (tests define the outcome, agent iterates until pass), external state, and graduation markers as code comments (`// via workflow, graduate to code when >500/day`).

---

## Design

`/super:design` is not only about craft. It is the full design system, from the first architectural question (does this layer need a visual surface at all?) through information architecture, direction, tokens, and interaction patterns for every channel.

**Init mode** runs 12 steps:

1. **Interface Modality Assessment** — for each EIID layer, determine: visual, conversational, notification, embedded, or none. Produce the EIID Interface Map. If no layers need a visual surface, write message structure, channel formatting, and agent interaction patterns only. Done.
2. **Detect UI Framework** — only if visual layers exist.
3. **Explore the Product's World** — domain concepts, color world, signature, defaults to reject.
4. **Direction Assessment** — references, anti-references, existing assets, constraints.
5. **Information Architecture** — core objects, navigation budget, screen map, focal points, content depth tiers. Before any visual decisions.
6. **Style Direction** — density, shape, weight.
7. **Typography Scale** — display through mono, with size, weight, line height, tracking.
8. **Layout Architecture** — grid, breakpoints, container strategy, page patterns.
9. **Composition Rules** — hierarchy, density map, section rhythm, proportion, whitespace.
10. **Token Layer** — extract before propose (scan code for repeated values, formalize).
11. **Interaction Patterns for Non-Visual Layers** — conversational craft (message structure, density per channel, timing) and agent interaction craft (transparency, clarification with defaults, handoff to visual, error communication).
12. **Write Design Configuration** — to CLAUDE.md and `.superskills/design-system.md`.

**Redesign mode** takes a target (file, screenshot, URL), runs a 6-layer strategic critique (alignment, composition, craft, content, structure, identity), applies craft dimensions grounded in the design system.

**Craft** covers 10 dimensions: direction spectrum, spatial composition, typography character, visual identity, subtle layering, atmosphere, motion, color intent, conversational/notification craft, agent interaction craft.

## Build

`/super:build` closes the loop from strategy to working code. It reads the EIID mapping, checks readiness (strategy, stack, design), decomposes the project into buildable pieces, and constructs each with a test-first loop.

Two types of pieces:

- **Shared standards** (auth, settings, logging, error handling, schema) follow patterns from `reference/shared-standards.md`. These are deterministic — consistency matters more than creativity. Every project needs them, and they should work identically every time.
- **Unique pieces** (interpretation visualizations, inference pipelines, agent flows, enrichment connectors) are generative. The LLM implements them, tests verify the outcome.

The build loop per piece: write acceptance tests → implement → verify (tests + types + design compliance) → log decision. Maximum 3 iterations. If still failing, stop and report.

**Init mode** builds from scratch: scaffold → schema → auth → layout → settings → EIID layers by priority → visual surfaces → agent runtime → background jobs.

**Extend mode** takes a target (feature, EIID layer, component) and adds it to the existing codebase through the same loop.

If the project uses LLM calls, workflows, or agents, shared standards include prompt visibility: every prompt is visible and editable by the user through settings. The intelligence layer is not a black box.

## Review

Seven audits. Tests run first (broken code makes other audits unreliable). The remaining six run in parallel when agent teams are available.

| Audit | Severity | Scope |
|-------|----------|-------|
| **Tests** | Blocking | vitest + Playwright setup and full suite |
| **Security** | Blocking on critical | OWASP Top 10, GDPR, secrets, stack-adaptive checks |
| **Build standards** | Blocking on auth/schema | Shared standards compliance (auth, schema, settings, error handling, logging, layout) |
| **Strategy** | Advisory | EIID alignment per file, scope creep, 11-question opportunity scan |
| **Design** | Blocking if widespread | Accessibility, IA, design system compliance, conversational/agent pattern compliance, consistency, framework rules, craft |
| **Performance** | Blocking on regression | Bundle, Core Web Vitals, N+1 queries, API costs |
| **Agent architecture** | Blocking on parity gaps | Implementation level, graduation readiness, tool quality, testing, feedback loop |

Agent architecture is skipped entirely if no EIID component uses agent, workflow, or LLM call. Build standards skips checks for features the project doesn't have.

## Skills and hooks

**Secrets guard** (hook, automatic): blocks hardcoded secrets on file write/edit. Ignores .env, config, migrations, lock files.

**EIID awareness** (skill, during planning): flags work that doesn't trace to an EIID layer. Checks implementation level mismatches (an LLM call using tool loops is under-classified, an agent that never iterates is over-classified). Checks parity: new UI actions need a corresponding agent tool. Nudges toward `/super:strategy` when CLAUDE.md is stale (untracked deps, unmapped files, shifted approaches, shifted implementation levels).

**Design awareness** (skill, during planning): flags registry alternatives, aesthetic drift, pattern reuse, IA violations, agent interaction consistency. Nudges toward `/super:design` when component patterns fall behind.

**Build awareness** (skill, during implementation): checks shared standards compliance when writing source code. Flags auth middleware gaps, unstructured errors, console.log logging, untested behavior, EIID traceability gaps, implementation level mismatches, and inline prompts that should be in config.

## Where things go

**CLAUDE.md** contains stable project instructions: context, stack, EIID mapping with strategic approach and implementation level per layer, technology constraints, design system config. It changes rarely. Updated by `/super:strategy` (init or refresh) and `/super:design` (Design System section). Claude reads it at session start. Target: under 100 lines.

**`.superskills/`** contains volatile findings:
- `report.md`: test results, security, build standards, design, performance, agent architecture findings. Build progress tracking. Replaced on each audit. Status counts at the top. Project Profile tracks recurring patterns.
- `decisions.md`: architecture decisions log. Append-only. Updated by `/super:strategy` refresh, `/super:review`, and `/super:design` redesign.
- `design-system.md`: EIID Interface Map, direction, references, IA, layout, typography scale, composition, tokens, component patterns, conversational patterns, agent interaction patterns. Updated by `/super:design` as the system evolves.

Commands **read** CLAUDE.md for context and **write** to `.superskills/`.

## Stack

`/super:strategy` recommends a stack for new projects based on the EIID mapping. For existing projects, it detects what's installed and adapts.

Five infrastructure roles:

| Role | Default | Criteria |
|------|---------|----------|
| Database + auth | Supabase | Managed, RLS, realtime |
| Hosting | Vercel | Edge, preview deployments |
| Workflows | Inngest | Scheduled jobs, retry, events |
| Frontend | Next.js | SSR, streaming |
| UI components | shadcn + Tailwind | Registry-based, token-driven |

Delivery channels (email, messaging, SMS) and enrichment tools (scraping, APIs) are added when the EIID mapping calls for them. Defaults are suggestions. The plugin adapts to any stack.

## Structure

```
superskills/                             the plugin
├── .claude-plugin/
│   ├── plugin.json                     plugin name: "super"
│   └── marketplace.json                marketplace definition
├── .githooks/
│   └── pre-commit                      auto-version on commit
├── commands/
│   ├── strategy.md                     assessment, EIID, implementation levels, agent architecture
│   ├── design.md                       12-step design system + redesign
│   ├── build.md                        autonomous build loop + extend mode
│   └── review.md                       six-domain audit
├── skills/
│   ├── eiid-awareness/SKILL.md          strategic alignment during planning
│   ├── design-awareness/SKILL.md        design compliance during planning
│   └── build-awareness/SKILL.md         shared standards + quality during implementation
├── agents/
│   └── stop-tests.md                    test runner (on-demand)
├── hooks/hooks.json                     secrets guard
├── reference/
│   ├── claude-md-template.md            CLAUDE.md structure
│   ├── design-system-template.md        design-system.md structure
│   ├── decisions-template.md            decisions.md structure
│   ├── design-critique.md              6-layer critique
│   ├── design-craft.md                 10 craft dimensions
│   ├── design-init-guide.md            modality assessment through tokens
│   ├── shared-standards.md             building block patterns (auth, settings, logging)
│   ├── build-readiness-guide.md        readiness gates for /super:build
│   ├── review-security-guide.md        security checklist
│   ├── review-performance-guide.md     performance checklist
│   └── examples/
│       ├── claude-md-saas.md            FleetPulse: visual-heavy, all code/buy
│       ├── design-system-saas.md        dense data, Nova style
│       ├── decisions-saas.md            SaaS decisions
│       ├── build-plan-saas.md          FleetPulse: build decomposition, all code/buy
│       ├── claude-md-consumer.md        RecipeBox: mixed modality, agents + workflows
│       ├── design-system-consumer.md    warm editorial, WhatsApp-first, agent interaction patterns
│       ├── decisions-consumer.md        consumer decisions
│       ├── build-plan-consumer.md      RecipeBox: build with agents, prompt management
│       ├── claude-md-devtool.md         DepWatch: no visual UI, single LLM call
│       ├── design-system-devtool.md     message structure only
│       ├── decisions-devtool.md         devtool decisions
│       └── build-plan-devtool.md       DepWatch: CLI build, no visual, LLM call
└── README.md

your-project/                            what gets generated
├── CLAUDE.md                            stable instructions (~100 lines)
└── .superskills/
    ├── report.md                        volatile findings (replaced each audit)
    ├── decisions.md                     architecture log (active + archived)
    └── design-system.md                 design direction + tokens + patterns
```

4 commands, 3 skills, 1 agent, 1 hook. 31 markdown files, 3 JSON. Three examples demonstrate the spectrum: FleetPulse (visual-heavy SaaS, all code/buy), RecipeBox (mixed modality consumer, agents and workflows with graduation), DepWatch (no-visual devtool, single LLM call with graduation to template).

## References

Strategic thinking draws from Wardley's value chain mapping and evolution axis, Choudary's platform dynamics and AI-driven value restructuring, Steinberger's intelligence-where-the-user-works principle, and the SaaSpocalypse pattern (commodity layers collapse, value moves to orchestration and delivery).

## License

MIT

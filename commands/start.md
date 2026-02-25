---
description: Start a project. Assessment, research, EIID mapping, writes CLAUDE.md and .superskills/.
allowed-tools: Read, Glob, Grep, Write, Edit, WebSearch, WebFetch
---

# SuperSkills — Project Start

## 1. Check State

If CLAUDE.md already has an EIID mapping, say so and suggest `/super:strategy` instead. Stop.

## 2. Assessment

Four questions. Ask one at a time. Wait for each answer before asking the next.

1. **Who is this for?** Company or client. Industry, size, geography.
2. **Who uses it?** The person, not the company. Role, what they do every day.
3. **What do they need?** A result, not a feature.
4. **What exists today?** Systems, data, tools, processes already in place.

## 3. Scan Folder

Scan the current directory for documents: md, txt, csv, json, pdf, images, docx, xlsx.
Skip node_modules, .git, dist, build, .next, .vercel.
Claude reads PDFs and images natively. Read every document found.

If documents add information the user didn't mention, incorporate it. If they contradict the user's answers, ask to clarify.

## 4. Research

Use the industry and geography from Q1 to search the web. Three searches:

1. **Industry landscape.** "[industry] trends [geography] [year]" — competitive dynamics, regulation, where the sector is heading.
2. **Value chain evolution.** "[industry] AI automation" — which activities are moving from custom to product to commodity? Which components have multiple interchangeable providers? Which still require novel work? This is Wardley's evolution axis: genesis → custom → product → commodity.
3. **Platform dynamics.** "[industry] platform disruption" or "[industry] value chain restructuring" — are intermediaries being bypassed? Are new aggregation points emerging? Is value shifting from execution to orchestration? Look for signs of Choudary's reshuffle: industries where AI collapses execution costs, making the ability to connect systems and coordinate flows the new source of value.

## 5. EIID Mapping

From the four answers, the folder scan, and the research, build the value chain and map it to EIID.

**Build the value chain.** Work backward from the user need (Q3) through the existing landscape (Q4) down to infrastructure. What depends on what? What's missing?

**Classify each component.** Use the research to determine evolution, not the user's opinion:
- **Commodity** (well-understood, multiple providers, standardized) → **Automate.** Don't build what you can buy.
- **Requires human judgment** (consequences, accountability, context-dependent) → **Differentiate.** Enhance with better information, keep humans in the loop.
- **New connections** (cross-system data, multi-source inference, orchestration that was previously too expensive) → **Innovate.** Build it — this is where value is created.

**Map to four layers:**

### Enrichment
- Existing data sources (list concrete systems)
- Missing data that is available (public APIs, partner data, user behavior)
- Connections to build

### Inference
- Patterns to detect (specific: "orders deviating >20% from historical average")
- Predictions to make
- Anomalies to flag

### Interpretation
- Insights to surface (actionable statements, not raw data)
- Framing: comparison, trend, explanation, recommendation

### Delivery
- Channels: email, Slack, WhatsApp, Telegram, SMS, Discord, dashboard
- Triggers: threshold crossed, schedule, event, user request
- Timing: when is the insight most valuable?

## 6. Recommend Stack

For new projects:

| Always | Role |
|--------|------|
| Supabase | Database, auth, storage, embeddings |
| Vercel | Hosting, edge functions |
| Inngest | Workflows, cron, retry |
| Next.js | Frontend, Server Components default |
| shadcn + Tailwind | UI components and styling |
| shadcnblocks registry | Pre-built sections via CLI: `npx shadcn add @shadcnblocks/<block>` |

| Conditional | When |
|-------------|------|
| Brevo | Email/SMS/WhatsApp delivery |
| Telegram/Slack/Discord SDK | Messaging delivery |
| Apify/Supermemory/Playwright | Enrichment, scraping |

For existing projects, detect stack from package.json and adapt recommendations.

## 7. Detect Constraints

If a package.json exists, read it. For each tool category where exactly one alternative is installed, generate a negative constraint to prevent Claude from suggesting the wrong tool.

Categories to check:
- ORM: Prisma, Drizzle, TypeORM, Kysely, Sequelize
- Test runner: Vitest, Jest, Mocha
- Linter: ESLint, Biome, oxlint
- CSS: Tailwind CSS, styled-components, Emotion, vanilla-extract
- State: Zustand, Jotai, Redux Toolkit, Valtio
- Backend: Supabase, Firebase, AWS Amplify
- Auth: NextAuth, Clerk, Lucia, Supabase Auth
- Server: Express, Fastify, Hono, Koa
- Package manager: detect from lock file (package-lock.json, pnpm-lock.yaml, yarn.lock, bun.lock)

Output format: "Use Prisma, NOT Drizzle, TypeORM, Kysely, Sequelize."

Write detected constraints to the Technology Constraints section of CLAUDE.md. If no package.json exists, skip this step.

## 8. Write CLAUDE.md

CLAUDE.md contains only stable project instructions. Findings go to `.superskills/`. Keep CLAUDE.md under 100 lines.

Create or update CLAUDE.md:

```
# [Project Name]

## Business
**Client:** [who is paying]
**Industry:** [sector, size, geography]

## User
**End user:** [role, daily context]
**Need:** [outcome, not feature]

## Stack
[Detected or recommended, with rationale]

## AI-Native Architecture (EIID)

### Enrichment
**Existing Data:** [sources]
**Missing Data:** [gaps]
**Connections:** [systems to integrate]

### Inference
**Patterns:** [what to detect]
**Predictions:** [what to forecast]
**Anomalies:** [what to flag]

### Interpretation
**Insights:** [what to surface]
**Framing:** [how to present]

### Delivery
**Channels:** [where]
**Triggers:** [when]
**Timing:** [optimal moment]

## Strategic Classification
**Automate:** [commodity — don't build what you can buy]
**Differentiate:** [human judgment — enhance with better information]
**Innovate:** [new connections — where value is being created]

## Technology Constraints
[detected constraints, one per line: "Use X, NOT Y, Z."]

## Design System
**Framework:** [detected or recommended]
**Token source:** [globals.css / theme.ts / etc.]
```

## 9. Write .superskills/

Create `.superskills/` directory with two files:

**`.superskills/report.md`** — volatile findings, replaced on each audit:
```
# SuperSkills Report

**Last review:** [date]
**Blockers:** [count or "none"]
**Warnings:** [count or "none"]

## Security Findings
[empty — populated by /super:trust or /super:review]

## Design Findings
[empty — populated by /super:design or /super:review]

## Test Report
[empty — populated by /super:test or /super:review]

## Performance Budget
[empty — populated by /super:performance or /super:review]

## Project Profile
**EIID Balance:** [which layers have the most code, which are underdeveloped]
**Recurring Patterns:** none yet
**Learned:** none yet
```

**`.superskills/decisions.md`** — architecture decisions log, append-only:
```
# Architecture Decisions

[empty — populated by /super:strategy]
```

Add `.superskills/` to the project's `.gitignore` suggestion list. The user decides whether to track it.

## 10. Suggest Next Steps

- `/super:strategy` to validate mapping and set priorities
- Has UI? `/super:design` for design system setup (shadcn + shadcnblocks registry, or detected framework)
- Handles user data? `/super:trust` for OWASP + GDPR
- Needs tests? `/super:test` for vitest + Playwright
- Has heavy deps? `/super:performance` for bundle + CWV budgets
- Full audit? `/super:review` for parallel review across all skills

## 11. Rules

- One conversation, then write. Do not iterate endlessly.
- Concrete items: "Gmail inbox" not "email data."
- Uncertain items get a question mark. The user refines later.
- CLAUDE.md is project instructions. `.superskills/` is findings. Keep them separate.
- The user answers four questions. Everything else — value chain, evolution, classification — you deduce from research and analysis.

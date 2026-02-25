---
description: Quick setup. Scan folder, read everything, see the project through Wardley + EIID eyes. Write CLAUDE.md and .superskills/.
allowed-tools: Read, Glob, Grep, Write, Edit, WebSearch, WebFetch
---

# SuperSkills — Scan

No questions, no web search. Read what's there and tell the user what this project could become.

## 1. Check State

If CLAUDE.md already has an EIID mapping, say so and suggest `/super:strategy` instead. Stop.

## 2. Scan Folder

Scan the current directory for documents: md, txt, csv, json, pdf, images, docx, xlsx.
Skip node_modules, .git, dist, build, .next, .vercel.
Claude reads PDFs and images natively. Read every document found.

## 3. Detect Stack

Read package.json if it exists. Identify:
- Framework (Next.js, Remix, Astro, SvelteKit, Nuxt, etc.)
- UI library (shadcn, Chakra, MUI, Mantine, Ant Design, Tailwind, etc.)
- Backend (Supabase, Firebase, AWS Amplify, Convex, etc.)
- ORM (Prisma, Drizzle, TypeORM, Kysely, etc.)
- Auth (NextAuth, Clerk, Lucia, Supabase Auth, etc.)
- Test runner (Vitest, Jest, Mocha, etc.)
- Linter (ESLint, Biome, oxlint, etc.)
- Package manager (from lock file)

## 4. Detect Constraints

For each tool category where exactly one alternative is installed, generate a negative constraint.

Output format: "Use Prisma, NOT Drizzle, TypeORM, Kysely, Sequelize."

## 5. Research

From what you've read, infer the industry and problem domain. Then search the web:

1. **Industry landscape.** "[inferred industry] trends [year]" — what's changing, what's being automated, what still requires human judgment.
2. **Value chain evolution.** "[inferred industry] AI automation" — which activities are moving from custom to commodity? What can you buy instead of build?
3. **Platform dynamics.** "[inferred industry] platform disruption" — is value shifting from execution to orchestration? Are intermediaries being bypassed? Are new aggregation points emerging?

If you can't confidently infer the industry, skip this step and note it in the analysis.

## 6. Read the Project Through Wardley + EIID Eyes

Now look at what you've read — code, docs, data, research — and answer these questions:

### Value chain
- **Who is the user?** Infer from the code: who is this built for? What are they trying to do?
- **What activities exist?** Map the code to a chain of activities from user need down to infrastructure. What depends on what?
- **What's missing?** Are there gaps in the chain — activities that should exist but don't?

### Evolution
- **What's commodity here?** Which components are well-understood, with many providers? (auth, storage, email delivery, basic CRUD). Are any of these built from scratch when they could be bought?
- **What's custom but shouldn't be?** Code that solves a problem already solved by a product or API.
- **What's genuinely novel?** Connections, analysis, or coordination that didn't exist before this project.

### EIID potential
Look at the project as it is today. Then ask: what could it become if it were AI-native?

- **Enrichment:** What data sources are connected? What data exists nearby but isn't being used? What cross-references would create new information?
- **Inference:** What patterns could be detected in the data that flows through this system? What predictions would help the user? What anomalies should be flagged?
- **Interpretation:** Where does raw data get shown to the user without context? What would turn data into actionable insights — comparison, trend, explanation, recommendation?
- **Delivery:** How do insights reach the user? Are they pulled (dashboard) or pushed (notification)? Could they be delivered where the user already works — email, Slack, WhatsApp, Telegram? What triggers would make them timely?

### Platform dynamics
- Is value concentrated in execution (doing things) or coordination (connecting things)?
- Are there intermediaries that could be bypassed?
- Could this system become a platform — connecting producers and consumers in a way that creates network effects?

Write all of this as a brief, opinionated analysis. Not a checklist. A narrative: "This is a [X] that does [Y]. The value chain runs from [user need] through [activities]. The [component] is commodity — don't build it. The opportunity is in [where EIID layers could add value]. If this were AI-native, it would [concrete description]."

## 7. Write CLAUDE.md

CLAUDE.md contains only stable project instructions. Keep it under 100 lines.

```
# [Project Name from package.json or folder name]

## Business
**Detected from:** folder scan (run /super:start for full assessment)
**Stack:** [detected stack]

## User
**Inferred user:** [who this seems built for]
**Inferred need:** [what they're trying to accomplish]

## AI-Native Architecture (EIID)

### Enrichment
**Connected:** [data sources found]
**Opportunity:** [what could be connected]

### Inference
**Present:** [analysis/detection found, or "none"]
**Opportunity:** [patterns, predictions, anomalies that could be added]

### Interpretation
**Present:** [insight formatting found, or "none"]
**Opportunity:** [how raw data could become actionable]

### Delivery
**Present:** [notification/messaging found, or "none"]
**Opportunity:** [channels, triggers, timing improvements]

## Strategic Classification
**Automate:** [commodity components — don't build what you can buy]
**Differentiate:** [components needing human judgment — enhance with better information]
**Innovate:** [new connections — where value is being created]

## Technology Constraints
[detected constraints, one per line]
```

## 8. Write .superskills/

Create `.superskills/` directory with two files:

**`.superskills/report.md`** — volatile findings, replaced on each audit:
```
# SuperSkills Report

**Last review:** none
**Blockers:** none
**Warnings:** none

## Security Findings

## Design Findings

## Test Report

## Performance Budget

## Project Profile
**EIID Balance:** [which layers are present/partial/missing]
**Recurring Patterns:** none yet
**Learned:** none yet
```

**`.superskills/decisions.md`** — architecture decisions log, append-only:
```
# Architecture Decisions

[empty — populated by /super:strategy]
```

## 9. Suggest Next Steps

- `/super:start` for full strategic assessment with business context and web research
- `/super:strategy` to validate the EIID mapping and set priorities
- `/super:trust` for security audit
- `/super:test` for test setup
- `/super:review` for full parallel audit

## 10. Rules

- Read everything, infer boldly, but mark inferences as inferences.
- Be opinionated. "This is commodity, don't build it" is more useful than "this could potentially be considered commodity."
- The narrative matters more than the checklist. The user should finish reading and think "now I see what this project could become."

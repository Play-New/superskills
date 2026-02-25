---
description: Quick setup. Scan folder, detect stack, write CLAUDE.md and .superskills/. No questions, no web search.
allowed-tools: Read, Glob, Grep, Write, Edit
---

# SuperSkills — Quick Scan

Fast entry point for existing projects. No assessment questions, no web search. Reads what's already there and writes CLAUDE.md with detected stack and constraints.

Use `/super:start` instead if you want the full assessment with EIID mapping and strategic classification.

## 1. Check State

If CLAUDE.md already has an EIID mapping, say so and suggest `/super:strategy` instead. Stop.

## 2. Scan Folder

Scan the current directory for documents: md, txt, csv, json, pdf, images, docx, xlsx.
Skip node_modules, .git, dist, build, .next, .vercel.
Read every document found. Build understanding of the project from what exists.

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

## 5. Infer EIID Layers

Based on folder structure and code, infer what exists for each layer:

- **Enrichment:** data sources, API integrations, import scripts, ETL
- **Inference:** analysis code, ML calls, pattern detection, aggregation
- **Interpretation:** formatting, templating, insight generation, summaries
- **Delivery:** notifications, messaging, webhooks, scheduled sends, dashboards

Mark each as: **present** (code exists), **partial** (some structure but incomplete), or **missing**.

Do not classify by constraint type (scarcity/risk/coordination). That requires the full assessment.

## 6. Write CLAUDE.md

CLAUDE.md contains only stable project instructions. Keep it under 100 lines.

```
# [Project Name from package.json or folder name]

## Context
**Detected from:** folder scan
**Stack:** [detected stack]

## AI-Native Architecture (EIID)

### Enrichment
[what was found or "not detected"]

### Inference
[what was found or "not detected"]

### Interpretation
[what was found or "not detected"]

### Delivery
[what was found or "not detected"]

## Technology Constraints
[detected constraints, one per line]
```

## 7. Write .superskills/

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

## 8. Suggest Next Steps

- `/super:start` for full strategic assessment with EIID mapping and classification
- `/super:trust` for security audit
- `/super:test` for test setup
- `/super:review` for full parallel audit

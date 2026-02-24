---
description: Generate HANDOFF.md with project state, EIID mapping, findings, and dependencies.
allowed-tools: Read, Glob, Grep, Write, Edit, Bash
---

# Handoff

Generate a complete HANDOFF.md for developer handoff.

## Steps

### 1. Read package.json

Extract: name, dependencies, devDependencies, scripts.

### 2. Detect Tech Stack

Check dependencies for notable packages:

| Package | Display Name |
|---------|-------------|
| @supabase/supabase-js | Supabase |
| stripe | Stripe |
| inngest | Inngest |
| prisma | Prisma |
| drizzle-orm | Drizzle |
| tailwindcss | Tailwind CSS |
| playwright | Playwright |
| vitest | Vitest |
| typescript | TypeScript |
| openai | OpenAI |
| @anthropic-ai/sdk | Anthropic SDK |

### 3. Detect Framework

Check in order: next (Next.js), nuxt (Nuxt), @sveltejs/kit (SvelteKit), @angular/core (Angular), vue (Vue), react (React), express (Express), fastify (Fastify).

### 4. Extract from CLAUDE.md

- EIID mapping (Enrichment, Inference, Interpretation, Delivery)
- Architecture Decisions
- Security Findings
- Design Findings
- Test Report
- Performance Budget
- Constraints ("Use X, NOT Y" lines)

### 5. Build and Test (optional)

Ask: "Run build and tests for the handoff report?" Skip with confirmation.

If yes:
- `npm run build` (30s timeout)
- `npm test` (60s timeout)

### 6. Write HANDOFF.md

```
# Handoff: [project name]

Generated: [date]

## Tech Stack

- [framework]
- [notable packages]

## Project Status

| Check | Status |
|-------|--------|
| Build | Pass / FAIL / Skipped |
| Tests | Pass / FAIL / Skipped |
| Claude Code configured | Yes / No |

## EIID Mapping

[extracted from CLAUDE.md]

## Skill Findings

### Security Findings
[from CLAUDE.md]

### Architecture Decisions
[from CLAUDE.md]

### Design Findings
[from CLAUDE.md]

### Test Report
[from CLAUDE.md]

### Performance Budget
[from CLAUDE.md]

## Technology Constraints

- Use X, NOT Y.
- Use A, NOT B.

## Dependencies

**Production** ([count]): [list, max 15, then "+ N more"]

**Dev** ([count]): [list, max 15, then "+ N more"]
```

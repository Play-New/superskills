---
description: Scan dependencies, detect drift, propose skill updates and negative constraints.
allowed-tools: Read, Glob, Grep, Write, Edit
---

# Evolve

Dependency drift detection. Always runs the same way.

## Steps

1. Read package.json. Detect package manager (npm/pnpm/yarn/bun from lock files).
2. Read CLAUDE.md. Skip recommendations already mentioned.
3. Check three tables below against installed dependencies.
4. Output report. Offer to append to CLAUDE.md.

---

## Table 1: Skill Implications

For each dependency found, add the recommendation to the report.

| Dependency | Skill | Recommendation |
|-----------|-------|----------------|
| stripe | trust | Enable PCI-DSS compliance checks for payment processing. |
| stripe | perf | Monitor Stripe API call costs and rate limits. |
| @supabase/supabase-js | trust | Verify Row Level Security policies on all tables. |
| @supabase/supabase-js | trust | Check that service_role key is never exposed to the client. |
| redis, ioredis, @upstash/redis | perf | Monitor cache hit rates and memory usage. |
| redis, ioredis, @upstash/redis | trust | Verify Redis connection uses TLS in production. |
| inngest | perf | Monitor Inngest function execution costs and retry rates. |
| inngest | test | Add integration tests for Inngest function handlers. |
| @clerk/nextjs, next-auth, lucia | trust | Verify auth middleware covers all protected routes. |
| @clerk/nextjs, next-auth, lucia | test | Add E2E tests for login/logout/session-expiry flows. |
| openai, @anthropic-ai/sdk | perf | Track LLM API costs per feature. Set budget alerts. |
| openai, @anthropic-ai/sdk | trust | Validate and sanitize all user input before sending to LLM. |
| @sendgrid/mail, @brevo/brevo, nodemailer, resend | trust | Verify email templates do not leak PII in headers or tracking. |
| @sendgrid/mail, @brevo/brevo, nodemailer, resend | strategy | Email delivery channel detected. Verify EIID Delivery layer includes email triggers. |
| @slack/web-api, @slack/bolt | strategy | Slack delivery channel detected. Verify EIID Delivery layer includes Slack triggers. |
| playwright | test | Ensure Playwright E2E tests cover all critical user flows. |
| prisma, drizzle-orm, kysely, typeorm | trust | Verify ORM queries use parameterized inputs. No raw SQL with user data. |
| prisma, drizzle-orm, kysely, typeorm | perf | Check for N+1 query patterns in data-heavy routes. |
| next | perf | Monitor bundle size. Check for client-side imports of server-only packages. |
| next | design | Verify all pages meet Core Web Vitals thresholds. |

---

## Table 2: Negative Constraints

For each group, if exactly one alternative is installed, generate a constraint: "Use X, NOT Y."

| Group | Alternatives |
|-------|-------------|
| ORM | Prisma, Drizzle, TypeORM, Kysely, Sequelize |
| Test runner | Vitest, Jest, Mocha |
| Linter | ESLint, Biome, oxlint |
| CSS | Tailwind CSS, styled-components, Emotion, vanilla-extract |
| State | Zustand, Jotai, Redux Toolkit, Valtio |
| Backend | Supabase, Firebase, AWS Amplify |
| Auth | NextAuth, Clerk, Lucia, Supabase Auth |
| Server | Express, Fastify, Hono, Koa |
| Package manager | npm, pnpm, Yarn, Bun |

Example output: "Use Prisma, NOT Drizzle, TypeORM, Kysely, Sequelize."

These constraints prevent Claude from suggesting wrong alternatives.

---

## Table 3: MCP Data Connectors

If the dependency is installed, suggest the MCP server.

| Dependency | MCP Server | Note |
|-----------|-----------|------|
| @supabase/supabase-js | Supabase MCP | Direct database queries and auth management. Data connector, not a skill replacement. |
| stripe | Stripe MCP | Payment data access and webhook inspection. Data connector, not a skill replacement. |
| @slack/web-api, @slack/bolt | Slack MCP | Message and channel data access. Data connector, not a skill replacement. |

---

## Output Format

```
## Evolve Report — [date]

### Skill Implications
- [skill]: [recommendation] (triggered by [dependency])

### Constraints
- Use [X], NOT [Y, Z].

### MCP Suggestions
- [MCP server]: [description]
```

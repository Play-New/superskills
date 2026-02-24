---
description: Start a project. Read docs, map to EIID, write CLAUDE.md.
allowed-tools: Read, Glob, Grep, Write, Edit
---

# Discovery and EIID Mapping

## 1. Check State

If CLAUDE.md already has an EIID mapping, suggest `/superskills:strategy` instead.

## 2. Read Everything

Scan the current directory for documents: md, txt, csv, json, pdf, images, docx, xlsx.
Skip node_modules, .git, dist, build, .next, .vercel.
Claude reads PDFs and images natively. Read every document found.

## 3. Conversation (only if docs are insufficient)

If documents cover the problem, skip to mapping. Otherwise, one round of questions. Two max. Skip what documents already answer.

1. **Problem.** What costs time, money, or attention today? Quantify: "4 hours/day on manual order entry" not "improve efficiency."
2. **Data.** What systems, APIs, files, databases exist? Gmail, ERP, CRM, spreadsheets, external APIs.
3. **People.** Who needs the output? Where are they? Email, Slack, WhatsApp, Telegram, dashboard, mobile.
4. **Outcome.** What does success look like in concrete terms?

## 4. EIID Mapping

Three ideas shape the mapping:

**Wardley evolution.** Components move from genesis (needs human judgment) to commodity (automate it). Classify each element: what can be automated today, what still requires human decision-making.

**Value movement (Choudary).** AI commoditizes processing. Value concentrates in orchestration and delivery. EIID ends with Delivery because that is where differentiation lives.

**Intelligence where the user is (Steinberger).** Deliver insights in channels people already use. Not dashboards they will ignore.

Map to four layers:

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

## 5. Recommend Stack

For new projects:

| Always | Role |
|--------|------|
| Supabase | Database, auth, storage, embeddings |
| Vercel | Hosting, edge functions |
| Inngest | Workflows, cron, retry |
| Next.js | Frontend, Server Components default |
| shadcnblocks > shadcn > Tailwind | UI hierarchy enforced by /superskills:design |

| Conditional | When |
|-------------|------|
| Brevo | Email/SMS/WhatsApp delivery |
| Telegram/Slack/Discord SDK | Messaging delivery |
| Apify/Supermemory/Playwright | Enrichment, scraping |

CLI over MCP. MCP servers are data connectors, not skill replacements. All tools GDPR-verified.

For existing projects, detect stack from package.json and adapt recommendations.

## 6. Write CLAUDE.md

Create or update CLAUDE.md with this structure:

```
# [Project Name]

## Context
**For:** [who]
**Business:** [what they do]
**Industry:** [sector]

## Problem
[Specific problem, quantified]

## Desired Outcome
[Concrete success criteria]

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

## Architecture Decisions

## Security Findings

## Design Findings

## Test Report

## Performance Budget
```

## 7. Suggest Next Steps

- `/superskills:strategy` to validate mapping and set priorities
- Has UI? `/superskills:design` for shadcn/shadcnblocks setup
- Handles user data? `/superskills:trust` for OWASP + GDPR
- Needs tests? `/superskills:test` for vitest + Playwright
- Has heavy deps? `/superskills:perf` for bundle + CWV budgets
- Want dependency drift detection? `/superskills:evolve`

## Rules

- One conversation, then write. Do not iterate endlessly.
- Concrete items: "Gmail inbox" not "email data."
- Uncertain items get a question mark. The user refines later.
- CLAUDE.md is the deliverable. Everything else is secondary.

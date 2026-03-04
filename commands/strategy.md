---
description: Entry point. Assessment, EIID mapping, scaffolding, priorities. First run builds the project foundation. With context about what's changed, refreshes the strategy.
allowed-tools: Read, Glob, Grep, Write, Edit, WebSearch, WebFetch
---

# Strategy

Read `reference/examples/claude-md-saas.md` for tone and structure. Match that level of specificity — type labels, EIID layer, concrete actions. Read `reference/examples/decisions-saas.md` for decision log format.

## Detect Mode

Two modes. Detection order matters.

**1. Does a strategy exist?** Check for CLAUDE.md with an EIID mapping section.

**2. Route:**
- **No EIID mapping** → run **init mode**
- **EIID mapping exists + user provided context about what changed** (business pivot, new users, new data sources, new constraints, market shift) → run **refresh mode**
- **EIID mapping exists + no context** → tell the user: "Strategy already exists. Provide context about what's changed to refresh it. Run `/review` for a full audit including strategy alignment."

---

## Init Mode

### 1. Scan Folder

Scan the current directory for documents: md, txt, csv, json, pdf, images, docx, xlsx.
Skip node_modules, .git, dist, build, .next, .vercel.
Claude reads PDFs and images natively. Read every document found.

### 2. Detect Stack and Constraints

Read package.json if it exists.

**Stack detection:** identify installed tools by category: framework, UI library, backend, ORM, auth, test runner, linter, package manager (from lock file). Name what's actually installed, not what could be.

**Constraint generation:** for each tool category where exactly one tool is installed, identify the popular alternatives in that same category and generate a negative constraint. The format: "Use [installed tool], NOT [alternatives]." Detect the package manager from the lock file (package-lock.json, pnpm-lock.yaml, yarn.lock, bun.lock).

### 3. Assessment

Six questions. Ask one at a time. Wait for each answer before asking the next. If the folder scan already surfaced relevant context, reference it in the question ("I see a Supabase setup and what looks like order data — who is this for?").

1. **Who is this for?** You, your company, or a client? What do they do, where?
2. **Who will use it?** Who will be the end user of your product?
3. **How do they work today?** What tools are on their desk? Where do they communicate (email, WhatsApp, Slack, phone)? How do they currently manage the data this product will use? What does a typical day look like? The intelligence layer wraps around existing behavior — this determines where enrichment collects and delivery returns.
4. **What do they want to achieve?** Not features — outcomes. Their goal, the value they're looking for.
5. **What's needed to deliver that value?** Tools, services, other products, skills.
6. **What exists already?** What data and tools do you have access to?

If documents from the folder scan add information the user didn't mention, incorporate it. If they contradict the user's answers, ask to clarify.

### 4. Research

Search the web close to the user's problem, not just the industry. One focused search:

**How this problem gets solved today.** "[user role] [problem] tools" or "[user need] software" — what products, workflows, and solutions exist? What's good, what's broken, what's missing? This grounds the EIID mapping in reality.

Use the research and your own knowledge to classify each component's evolution stage in step 5. What's commodity (multiple providers, standardized)? What requires human judgment? Where are new connections possible that were previously too expensive? The classification comes from informed judgment, not from separate searches.

### 5. EIID Mapping

From the folder scan, stack detection, user context, and research, build the value chain and map it to EIID.

**Build the value chain.** Work backward from the user need through what's needed and what exists down to infrastructure. What depends on what? What's missing?

**Classify each component.** Use the research to determine evolution, not the user's opinion:
- **Commodity** (well-understood, multiple providers, standardized) → **Automate.** Don't build what you can buy.
- **Requires human judgment** (consequences, accountability, context-dependent) → **Differentiate.** Enhance with better information, keep humans in the loop.
- **New connections** (cross-system data, multi-source inference, orchestration that was previously too expensive) → **Innovate.** Build it — this is where value is created.

**Map to four layers:**

#### Enrichment (collect)
- Existing data sources (list concrete systems)
- Human input channels: how do people naturally feed data? (photos, voice notes, chat messages, forwarded emails, manual entry). People should not change their behavior.
- Missing data that is available (public APIs, partner data, user behavior)
- Connections to build

#### Inference (patterns)
- Patterns to detect (specific: "orders deviating >20% from historical average")
- Predictions to make
- Anomalies to flag

#### Interpretation (insights)
- Insights to surface (actionable statements, not raw data)
- Framing: comparison, trend, explanation, recommendation

#### Delivery (reach)
- Channels: same channels people use for input. Email, Slack, WhatsApp, Telegram, SMS, Discord. Input and output share channels. The system is an invisible layer, not a destination.
- Triggers: threshold crossed, schedule, event, user request
- Timing: when is the insight most valuable?

**Platform dynamics** as closing observation: is value concentrated in execution or coordination? Could this become a platform? Are intermediaries being bypassed?

### 6. Stack Recommendation

For existing projects, confirm detected stack and adapt recommendations.

For new projects, suggest a stack that fills these roles. The user may choose differently.

| Role | Default | Description |
|------|---------|-------------|
| Full-stack framework with SSR | Next.js | Server-rendered pages, API routes, file-based routing |
| Managed database with auth and realtime | Supabase | Database, authentication, storage, realtime subscriptions |
| Workflow engine for scheduled jobs | Inngest | Background tasks, cron, retry logic, event-driven workflows |
| Edge hosting with preview deployments | Vercel | Global CDN, serverless functions, branch previews |
| Component library with token-driven styling | shadcn + Tailwind | Pre-built accessible components, design token integration |

| Conditional Role | Default | When |
|-------------------|---------|------|
| Email/SMS delivery service | Brevo | Delivery layer uses email or SMS channels |
| WhatsApp messaging library | Baileys | Delivery layer uses WhatsApp |
| Chat platform SDK | Platform-specific SDK | Delivery layer uses Telegram, Slack, Discord, or similar |
| Web scraping/automation tool | Firecrawl | Enrichment layer needs external data collection |

If the user prefers a different tool for any role, use it. Generate technology constraints from whatever stack is chosen.

### 7. Set Priorities

1. Validate the EIID mapping. Flag anything vague or missing.
2. Classify each EIID element:
   - **Automate** (scarcity-based value collapsing): well-understood, many providers, AI eroding the advantage
   - **Differentiate** (risk-based value increasing): requires human judgment, benefits from better information
   - **Innovate** (coordination-based value emerging): newly possible connections between systems, teams, data
3. Set priorities: automate first (quick wins), innovate flagged for careful design.
4. Write the first Architecture Decision to `.superskills/decisions.md`.

### 8. Write CLAUDE.md

CLAUDE.md contains only stable project instructions. Findings go to `.superskills/`. Keep CLAUDE.md under 100 lines.

Follow `reference/claude-md-template.md` for structure. Read `reference/examples/claude-md-saas.md` for tone and level of specificity.

**Before writing, show the user the full CLAUDE.md you intend to create.** Ask for confirmation. Incorporate feedback. Only write the file after approval.

### 9. Write .superskills/

Create `.superskills/` directory. Step 7 already created `.superskills/decisions.md` with the first architecture decision. Create the remaining file:

**`.superskills/report.md`** — volatile findings, replaced on each audit:
```
# SuperSkills Report

**Last review:** [date]
**Blockers:** [count or "none"]
**Warnings:** [count or "none"]

## Security Findings
[empty — populated by /super:review]

## Design Findings
[empty — populated by /super:review]

## Test Report
[empty — populated by /super:review]

## Performance Budget
[empty — populated by /super:review]

## Project Profile
**EIID Balance:** [which layers have the most code, which are underdeveloped]
**Recurring Patterns:** none yet
**Learned:** none yet
```

Add `.superskills/` to the project's `.gitignore` suggestion list. The user decides whether to track it.

### 10. What's Next

Tell the user:

**Next step:** run `/super:design` for design system setup, or `/super:review` for a full quality audit (tests, security, strategy alignment, design consistency, performance).

Then start building. The plugin watches what you do. When you want a check, run `/super:review` for a full audit — all at once.

---

## Refresh Mode

Re-assessment with new context. Something changed in the business, users, data, or market. Consistency audits belong in `/review`.

### 1. Load Existing Context

Read CLAUDE.md and `.superskills/decisions.md`. Understand the current EIID mapping, stack, user context, and decision history.

### 2. Change Assessment

Show the user what the current strategy says (key elements: business, user, EIID layers, approach classifications). Then collect input in a single block. The user answers what's relevant, skips what isn't.

**What changed:** "What's different from when the strategy was created? Business direction, target users, data sources, market, constraints."

**What still holds:** "Anything that should stay exactly as is?"

**New information:** "Any new research, competitors, tools, or opportunities discovered since the last strategy?"

Skip questions the user already answered in their initial request.

### 3. Research

Search the web focused on what changed. If the user says "new competitor" or "different market", research that. If data sources changed, research what's available. Same approach as init mode step 4, but scoped to the delta.

### 4. Update EIID Mapping

Rebuild the value chain from the updated context. For each component:
- **Still valid?** Keep as is.
- **Evolution changed?** Something that was "innovate" might now be commodity (competitors caught up). Something classified as "automate" might need custom work (off-the-shelf doesn't fit anymore). Reclassify.
- **New components?** New data sources, new inference patterns, new delivery channels. Add them.
- **Removed?** Components that no longer apply. Remove them.

Map to the same four layers (Enrichment, Inference, Interpretation, Delivery) with updated content.

### 5. Update Priorities

Reclassify each EIID element: automate / differentiate / innovate. Set updated priorities. What changed from last time?

### 6. Rewrite CLAUDE.md

Follow the same structure as init mode step 8. **Show the full updated CLAUDE.md to the user before writing.** Highlight what changed from the previous version. Ask for confirmation.

### 7. Log Decision

Append to `.superskills/decisions.md`:

```
### [date] - Strategy Refresh

**Type:** decision
**Summary:** [what changed and why]
**Changes:** [list of EIID mapping changes, reclassifications, new/removed components]
**Action:** [updated priorities]
```

---

## Rules

- One conversation, then write. Do not iterate endlessly.
- Concrete items: "Gmail inbox" not "email data."
- Uncertain items get a question mark. The user refines later.
- Mark inferences as inferences.
- CLAUDE.md is project instructions. `.superskills/` is findings. Keep them separate.
- In init mode, the user answers questions or confirms inferences. Everything else — value chain, evolution, classification — you deduce from research and analysis.

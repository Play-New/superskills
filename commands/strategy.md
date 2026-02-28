---
description: Entry point. Assessment, EIID mapping, scaffolding, priorities. First run builds the project foundation. After that, alignment review and opportunity scan.
allowed-tools: Read, Glob, Grep, Write, Edit, WebSearch, WebFetch
---

# Strategy

Read `reference/examples/claude-md-saas.md` for tone and structure. Match that level of specificity — type labels, EIID layer, concrete actions. Read `reference/examples/decisions-saas.md` for decision log format.

## Detect Mode

Check for CLAUDE.md with an EIID mapping section. If it has one, run **review mode**. Otherwise, run **init mode**.

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

Five questions. Ask one at a time. Wait for each answer before asking the next. If the folder scan already surfaced relevant context, reference it in the question ("I see a Supabase setup and what looks like order data — who is this for?").

1. **Who is this for?** You, your company, or a client? What do they do, where?
2. **Who will use it?** Who will be the end user of your product?
3. **What do they want to achieve?** Not features — outcomes. Their goal, the value they're looking for.
4. **What's needed to deliver that value?** Tools, services, other products, skills.
5. **What exists already?** What data and tools do you have access to?

If documents from the folder scan add information the user didn't mention, incorporate it. If they contradict the user's answers, ask to clarify.

### 4. Research

Search the web close to the user's problem, not just the industry. Three searches:

1. **How this problem gets solved today.** "[user role] [problem] tools" or "[user need] software" — what products, workflows, and solutions exist? What's good, what's broken, what's missing? This grounds the EIID mapping in reality.
2. **What's commoditizing.** "[problem domain] AI automation [year]" — which parts of this solution space are becoming commodity? What can you buy instead of build? Where is the evolution axis (Wardley: genesis → custom → product → commodity) moving fastest?
3. **Where value is shifting.** "[problem domain] platform" or "[industry] value chain restructuring" — are intermediaries being bypassed? Is value moving from execution to orchestration? Look for Choudary's reshuffle: where AI collapses execution costs, the ability to connect systems and coordinate flows becomes the new source of value.

Use the assessment answers as context, but keep the searches anchored to the end user and their need.

### 5. EIID Mapping

From the folder scan, stack detection, user context, and research, build the value chain and map it to EIID.

**Build the value chain.** Work backward from the user need through what's needed and what exists down to infrastructure. What depends on what? What's missing?

**Classify each component.** Use the research to determine evolution, not the user's opinion:
- **Commodity** (well-understood, multiple providers, standardized) → **Automate.** Don't build what you can buy.
- **Requires human judgment** (consequences, accountability, context-dependent) → **Differentiate.** Enhance with better information, keep humans in the loop.
- **New connections** (cross-system data, multi-source inference, orchestration that was previously too expensive) → **Innovate.** Build it — this is where value is created.

**Map to four layers:**

#### Enrichment (data)
- Existing data sources (list concrete systems)
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
- Channels: email, Slack, WhatsApp, Telegram, SMS, Discord, dashboard
- Triggers: threshold crossed, schedule, event, user request
- Timing: when is the insight most valuable?

**Platform dynamics** as closing observation: is value concentrated in execution or coordination? Could this become a platform? Are intermediaries being bypassed?

### 6. Stack Recommendation

For existing projects, confirm detected stack and adapt recommendations.

For new projects, suggest this stack as a starting point. The user may choose differently.

| Suggested | Role |
|-----------|------|
| Supabase | Database, auth (email/password or magic link + Google OAuth), storage, embeddings |
| Vercel | Hosting, edge functions |
| Inngest | Workflows, cron, retry |
| Next.js | Frontend, Server Components default |
| shadcn + Tailwind | UI components and styling |

| Conditional | When |
|-------------|------|
| Brevo | Email/SMS delivery |
| Baileys | WhatsApp delivery |
| Telegram/Slack/Discord SDK | Messaging delivery |
| Apify/Playwright | Enrichment, scraping |

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

Read `reference/examples/claude-md-saas.md` for tone and structure. Match that level of specificity.

**Before writing, show the user the full CLAUDE.md you intend to create.** Ask for confirmation. Incorporate feedback. Only write the file after approval.

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

## EIID

### Enrichment (data)
**Have:** [sources already connected]
**Missing:** [gaps to fill]
**Connect:** [systems to integrate]
**Approach:** [automate / differentiate / innovate — per component]

### Inference (patterns)
**Detect:** [what to spot]
**Predict:** [what to forecast]
**Flag:** [anomalies to catch]
**Approach:** [automate / differentiate / innovate — per component]

### Interpretation (insights)
**Surface:** [what to tell the user]
**Frame as:** [comparison, trend, explanation, recommendation]
**Approach:** [automate / differentiate / innovate — per component]

### Delivery (reach)
**Channels:** [where the user already is]
**Triggers:** [when to send]
**Timing:** [optimal moment]
**Approach:** [automate / differentiate / innovate — per component]

## Technology Constraints
[detected constraints, one per line: "Use X, NOT Y, Z."]

## Code Architecture
- No source file over 200 lines. Split by responsibility.
- One component per file. One utility per file.
- Colocation: tests next to source, types next to usage.
- Prefer composition over inheritance.
- If a module has two distinct modes, split into separate files.

## Design System
**Framework:** [detected or recommended]
**Token source:** [globals.css / theme.ts / etc.]
```

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
[empty — populated by /super:design or /super:review]

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

## Review Mode

### Alignment Check

For each file changed recently (use git diff or scan src/):
1. Which EIID layer does it support? (enrichment / inference / interpretation / delivery / none)
2. If "none": is it supporting infrastructure (tests, types, config, build, utilities used by EIID layers)? Expected, not scope creep. If it's application code that doesn't serve any EIID layer, flag as potential scope creep.
3. Any dependency not traceable to the EIID mapping? Flag it.

### Opportunity Scan

Check each item against the codebase. Answer yes or no. If yes, describe the opportunity in one sentence.

**Enrichment:**
1. Any data source connected but not used for inference?
2. Any two data sources that could be cross-referenced but are not?
3. Any public API or external data that would fill a gap in the current schema?

**Inference:**
4. Any collected data with patterns not being analyzed?
5. Any prediction the existing data supports but nobody built?
6. Any anomaly detection that would catch problems before users notice?

**Interpretation:**
7. Any analysis result generated but not surfaced to users?
8. Any insight that lacks context (trend, comparison, explanation) to be actionable?

**Delivery:**
9. Any channel the users frequent that the system does not reach?
10. Any timing improvement (deliver sooner, deliver at the right moment)?
11. Any trigger condition that would catch events currently missed?

### CLAUDE.md Refresh

After the alignment check and opportunity scan, compare CLAUDE.md against the actual codebase. Check for drift:

1. **Stack:** read package.json. Are there dependencies not reflected in the Stack section? Are documented tools no longer installed? Update the Stack section and regenerate Technology Constraints.
2. **EIID mapping:** are there EIID layers documented but empty in code, or developed in code but missing from the mapping? Has a component's approach changed (e.g., something classified as "innovate" is now commodity, or "automate" was custom-built instead)? Update the mapping to reflect what actually exists.
3. **User/Business:** if the alignment check or opportunity scan revealed a shift in user need or business context, flag it. Do not auto-update these sections without user confirmation.

If drift is found in 1-2: show the proposed CLAUDE.md changes, get user confirmation, then apply. If drift is found in 3: flag it and ask the user.

If no drift is found, skip this step.

### Output

Read CLAUDE.md for project context. **Append** each finding to `.superskills/decisions.md` (append-only — it is a decision log, history matters):

```
### [date] - [title]

**Type:** alignment-check | opportunity | drift-warning | decision
**Summary:** [1-2 sentences]
**EIID Layer:** [enrichment / inference / interpretation / delivery / none]
**Action:** [what to do about it]
```

---

## Rules

- One conversation, then write. Do not iterate endlessly.
- Concrete items: "Gmail inbox" not "email data."
- Uncertain items get a question mark. The user refines later.
- Mark inferences as inferences.
- CLAUDE.md is project instructions. `.superskills/` is findings. Keep them separate.
- In init mode, the user answers questions or confirms inferences. Everything else — value chain, evolution, classification — you deduce from research and analysis.

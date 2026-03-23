---
description: Entry point. Assessment, EIID mapping, scaffolding, priorities. First run builds the project foundation. With context about what's changed, refreshes the strategy.
allowed-tools: Read, Glob, Grep, Write, Edit, WebSearch, WebFetch
---

# Strategy

Read `reference/concepts.md` for EIID layer definitions, implementation levels, and graduation pattern. Those are canonical — don't redefine them here.

## Detect Mode

Two modes. Detection order matters.

**1. Does a strategy exist?** Check for CLAUDE.md with an EIID mapping section.

**2. Route:**
- **No EIID mapping** -> run **init mode**
- **EIID mapping exists + user provided context about what changed** (business pivot, new users, new data sources, new constraints, market shift) -> run **refresh mode**
- **EIID mapping exists + no context** -> tell the user: "Strategy already exists. Provide context about what's changed to refresh it. Run `/super:review` for a full audit including strategy alignment."

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

You need to understand six things before you can build an EIID mapping:

1. **Who pays and who uses.** The client, the end user, their daily context.
2. **Current workflow.** What tools are on their desk, where they communicate (email, WhatsApp, Slack, phone), how they manage the data this product will touch. Intelligence wraps around existing behavior — this determines where enrichment collects and delivery returns.
3. **Desired outcome.** Not features — the value they want. What changes for them when this works.
4. **Target feeling.** The emotional state when the product works perfectly. Not "satisfied" — specific: calm control, warm relief, precise confidence. This becomes the quality benchmark for every downstream decision.
5. **Available resources.** Data, tools, services, skills they already have access to.
6. **Constraints.** Budget, timeline, team size, compliance, existing commitments.

The folder scan already answered some of these. Think about what you learned from the documents and code, then ask only what's still missing. Reference what you found: "I see a Supabase setup with what looks like order data — who is this for and who uses it?" Combine questions where natural. If the scan gave you most of what you need, you might ask two questions. If the folder was empty, you might ask five.

If documents from the folder scan add information the user didn't mention, incorporate it. If they contradict the user's answers, ask to clarify.

### 4. Readiness Check

Before research, check for prior context:
- Read `.superskills/report.md` if it exists. The Project Profile section captures recurring patterns from previous reviews — use them to avoid repeating known issues in the new strategy.

### 5. Research

Search the web close to the user's problem, not just the industry. Three focused searches, each building on the previous:

**Search 1 — How this problem gets solved today.** "[user role] [problem] tools" or "[user need] software" — what products, workflows, and solutions exist? What's good, what's broken, what's missing? This grounds the EIID mapping in reality.

**Search 2 — Who does it well.** From search 1, identify the 2-3 best existing solutions. Search for them specifically: their architecture, pricing, limitations, user complaints. What do they get right? Where do users say they fall short? This reveals where commodity ends and differentiation begins.

**Search 3 — What changed recently.** "[problem domain] AI tools [current year]" or "[industry] automation [current year]" — what's new in this space? What was custom six months ago and is now a service? What services shut down or pivoted? This prevents building something that's already commodity or betting on a dying tool.

Use the research and your own knowledge to classify each component's evolution stage in step 6. What's commodity (multiple providers, standardized)? What requires human judgment? Where are new connections possible that were previously too expensive? The classification comes from informed judgment, not from separate searches.

**Reference seeds for design.** From the research, note 2-3 products with interfaces worth studying — not necessarily competitors, but products whose users have similar contexts (same urgency, same density needs, same device usage). Write these to `.superskills/decisions.md` as a decision entry (type: `research`, EIID Layer: `Interpretation`) so they persist and `/super:design` step 3 can read them.

### 6. EIID Mapping

From the folder scan, stack detection, user context, and research, build the value chain and map it to EIID.

**Minimal surface applies to the mapping itself.** Every component must trace to the user need. Challenge each one: is this needed to deliver the value, or is it "nice to have"? A mapping with 4 tightly connected components beats one with 12 loosely justified ones. If a component exists because "products like this usually have it", remove it.

**Build the value chain.** Work backward from the user need through what's needed and what exists down to infrastructure. What depends on what? What's missing?

**Classify each component.** Use the research to determine evolution:
- **Commodity** (well-understood, multiple providers, standardized) -> **Automate.** Don't build what you can buy.
- **Requires human judgment** (consequences, accountability, context-dependent) -> **Differentiate.** Enhance with better information, keep humans in the loop.
- **New connections** (cross-system data, multi-source inference, orchestration that was previously too expensive) -> **Innovate.** Build it — this is where value is created.

**For each component, determine the implementation level.** See `reference/concepts.md` for level definitions. Record in each layer's Approach field: `[Automate / Differentiate / Innovate] via [level]. Graduation: [trigger, optional]`. Graduation triggers are optional for commodity components but recommended for LLM call, workflow, and agent levels.

**Record the target feeling** in the EIID mapping header. This is the emotional benchmark for every decision that follows. Example: "Target feeling: calm control — the fleet manager glances and knows everything is fine."

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
- Channels: same channels people use for input. The system is an invisible layer, not a destination.
- Triggers: threshold crossed, schedule, event, user request
- Timing: when is the insight most valuable?

**Platform dynamics** as closing observation: is value concentrated in execution or coordination? Could this become a platform? Are intermediaries being bypassed?

### 6b. Agent Architecture

Skip this step entirely if no EIID component uses agent, workflow, or LLM call.

For components classified via agent, workflow, or LLM call:

- **Orchestration pattern:** single agent with tools, pipeline of fixed steps, or router that dispatches to specialized agents? Start with the simplest. A single agent with 5 tools beats a multi-agent system for most products.
- **Tool design:** each tool does one atomic thing. "process order" is too coarse. "look up product", "check inventory", "create order" compose into process order. Tools are primitives, not features.
- **Parity check:** every action a user can take via UI should also be available as a tool for agents. List the gaps.
- **Cost model:** tokens per invocation x invocations per day = monthly cost. Compare with the cost of coding the same behavior. If the agent costs more than a junior developer's time, graduate to code.
- **State persistence:** where does agent state live? Database, filesystem, git. Never use conversation history as the primary store.

### 7. Stack Recommendation

For existing projects, confirm detected stack and adapt recommendations.

For new projects, derive from the EIID mapping which roles need filling. Not every project needs every role. A CLI tool needs no framework, no hosting, no component library. A WhatsApp-only product needs a messaging library and a database but no frontend. A visual SaaS needs most roles. The mapping tells you.

**Common roles** (only when the product needs them):
- Full-stack framework — when the product has visual surfaces with server-rendered pages and API routes
- Database with auth — when the product persists user data
- Background job runner — when the product has scheduled tasks, retries, or event-driven workflows
- Hosting with CI/CD — when the product needs deployment and preview environments
- Component library — when visual surfaces need pre-built accessible components
- Messaging library — when delivery uses WhatsApp, Telegram, or similar channels
- Agent runtime — when components classified as "agent" need memory, scheduling, and channel access

For each role the product needs, research and recommend the best fit for the project's context. Consider: team familiarity, ecosystem maturity, integration with other chosen tools, pricing at projected scale, and how well the tool supports AI-assisted development. Present recommendations with reasoning, not as mandates.

### 8. Set Priorities

1. Validate the EIID mapping. Flag anything vague or missing.
2. Classify each EIID element:
   - **Automate** (scarcity-based value collapsing): well-understood, many providers, AI eroding the advantage
   - **Differentiate** (risk-based value increasing): requires human judgment, benefits from better information
   - **Innovate** (coordination-based value emerging): newly possible connections between systems, teams, data
3. Set priorities: automate first (quick wins), innovate flagged for careful design.
4. Write the first decision to `.superskills/decisions.md` (type: `decision`).

### 9. Write CLAUDE.md

CLAUDE.md contains only stable project instructions. Findings go to `.superskills/`. Keep it under 100 lines.

**Structure** (use `reference/claude-md-template.md` for output format):
- **Business** — client, industry, geography. One line each.
- **User** — end user role, daily context, need (outcome not feature), target feeling.
- **Stack** — detected or recommended, with rationale.
- **EIID** — four layers. Each layer lists concrete items (Have/Missing/Connect for Enrichment, Detect/Predict/Flag for Inference, Surface/Frame for Interpretation, Channels/Triggers/Timing for Delivery) plus Approach field with classification, level, and graduation trigger.
- **Technology Constraints** — one per line, "Use X, NOT Y, Z."
- **Code Architecture** — 3-5 architectural decisions specific to this project, derived from the EIID mapping and stack. Not generic advice. If EIID includes agent/workflow/LLM call components: add parity, granularity, tests-as-reward-signal, external state, and graduation markers.
- **Design System** — framework, style, token source, direction, navigation, typography, color character, signature. Filled by `/super:design`, left as placeholders if running strategy alone.

The tone is terse, specific, concrete. Type labels and EIID layers on every item. Actions, not descriptions.

**Before writing, show the user the full CLAUDE.md you intend to create.** Ask for confirmation. Incorporate feedback. Only write the file after approval.

### 10. Write .superskills/

Create `.superskills/` directory. Step 8 already created `.superskills/decisions.md` with the first architecture decision. Create the remaining file:

**`.superskills/report.md`** — volatile findings, replaced on each audit. Follow the structure in `reference/report-template.md`.

Add `.superskills/` to the project's `.gitignore` suggestion list. The user decides whether to track it.

### 11. What's Next

Tell the user:

**Next step:** run `/super:design` for design system setup, or `/super:build` to start constructing from the strategy. Run `/super:review` for a full quality audit.

If the research (step 5) identified reference products worth studying for design, mention them: "During research, [Product A] and [Product B] stood out as good design references for [reason]. Consider them when running `/super:design`."

Skills advise as you work. When you want a full check, run `/super:review` for a complete audit — all at once.

---

## Refresh Mode

Re-assessment with new context. Something changed in the business, users, data, or market. Consistency audits belong in `/super:review`.

### 1. Load Existing Context

Read CLAUDE.md, `.superskills/decisions.md`, and `.superskills/report.md` (if it exists — the Project Profile section captures recurring patterns from previous reviews). Understand the current EIID mapping, stack, user context, and decision history. Focus on **Active** decisions — archived decisions are context, not constraints.

### 2. Change Assessment

Show the user what the current strategy says (key elements: business, user, target feeling, EIID layers, approach classifications). Then collect input in a single block. The user answers what's relevant, skips what isn't.

**What changed:** "What's different from when the strategy was created? Business direction, target users, data sources, market, constraints."

**Target feeling:** "The current target feeling is '[read from CLAUDE.md]'. Does this still describe how users should feel? A business pivot, a new user segment, or a shift in product scope can change the emotional benchmark."

**What still holds:** "Anything that should stay exactly as is?"

**New information:** "Any new research, competitors, tools, or opportunities discovered since the last strategy?"

Skip questions the user already answered in their initial request.

### 3. Research

Search the web focused on what changed. If the user says "new competitor" or "different market", research that. If data sources changed, research what's available. Same approach as init mode step 5, but scoped to the delta.

### 4. Update EIID Mapping

Rebuild the value chain from the updated context. For each component:
- **Still valid?** Keep as is.
- **Evolution changed?** Something that was "innovate" might now be commodity (competitors caught up). Something classified as "automate" might need custom work (off-the-shelf doesn't fit anymore). Reclassify.
- **New components?** New data sources, new inference patterns, new delivery channels. Add them.
- **Removed?** Components that no longer apply. Remove them.

Map to the same four layers with updated content.

**Update the target feeling.** If the user confirmed the feeling still holds, keep it. If it changed, record the new target feeling in the EIID mapping header. The target feeling drives design direction, experience patterns, and build verification — changing it cascades through everything downstream. If it changed, flag: "Target feeling changed. Run `/super:design` to update experience patterns."

### 5. Update Priorities

Reclassify each EIID element: automate / differentiate / innovate. Set updated priorities. What changed from last time?

### 6. Rewrite CLAUDE.md

Follow the same structure as init mode step 9. **Show the full updated CLAUDE.md to the user before writing.** Highlight what changed from the previous version. Ask for confirmation.

### 7. Archive and Log

**Archive stale decisions.** Scan the Active section of `.superskills/decisions.md`. For each active decision, check: does the context that justified it still hold given the new information? If not, move it to the Archived section with the date and reason.

**Log the refresh decision.** Append a new entry to the Active section of `.superskills/decisions.md`. Follow the format in `reference/decisions-template.md`. Summarize what changed in this refresh and why.

---

## Rules

- One conversation, then write. Do not iterate endlessly.
- Concrete items: "Gmail inbox" not "email data."
- Uncertain items get a question mark. The user refines later.
- Mark inferences as inferences.
- CLAUDE.md is project instructions. `.superskills/` is findings. Keep them separate.
- In init mode, the user answers questions or confirms inferences. Everything else — value chain, evolution, classification — you deduce from research and analysis.

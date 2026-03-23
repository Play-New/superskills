---
description: Autonomous build from strategy and design. Plan once, approve, then fully autonomous construction with design-first principles. Stops only on hard failure.
allowed-tools: Read, Glob, Grep, Write, Edit, Bash, WebSearch, WebFetch
---

# Build

Read `reference/build-principles.md` before every build decision.

## Rule Zero

Before adding anything — a screen, a component, an endpoint, a table, a setting, a dependency — ask:

1. **Does this trace to the EIID mapping?** If not, don't build it.
2. **Is there a simpler way?** Fewer screens. Fewer components. Fewer abstractions. The simplest version that delivers the value.
3. **Does this earn its place?** Every element a user sees, every endpoint, every table column must justify its existence against the user need. If removing it wouldn't hurt the outcome, remove it.

This is not a UI guideline. It applies to everything: data models, API routes, background jobs, agent tools, visual surfaces. A product with 3 screens that each do one thing perfectly beats a product with 12 screens. A schema with 4 tables that model the real relationships beats 10 tables that model hypothetical ones.

Rule zero overrides all other build decisions. When in doubt, build less.

## Detect Mode

**1. Does a strategy exist?** Check for CLAUDE.md with an EIID mapping section.

**2. Route:**
- **No EIID mapping** → stop. "No strategy found. Run `/super:strategy` first."
- **EIID mapping exists + no built code** (empty src/ or app/) → run **init mode**
- **EIID mapping exists + built code + user provided a target** → run **extend mode**
- **EIID mapping exists + built code + no target** → "Project has code. Provide a target to build."

---

## Init Mode

### 1. Readiness

Read CLAUDE.md, `.superskills/design-system.md`, `.superskills/decisions.md`.

Three gates:

- **Strategy:** EIID mapping with Approach fields on all layers.
- **Stack:** package.json with framework installed. If missing, install from CLAUDE.md.
- **Design:** if any EIID layer needs a visual surface, `.superskills/design-system.md` must have Direction and Tokens. If no visual layers, skip.

Fail → tell the user what's missing and which command to run. Stop.

**Human prerequisites.** Check once:
- Git repo, Node.js, package manager on PATH
- Service credentials in `.env.local` for each service in the EIID mapping (database, APIs, LLM provider)
- Missing? Tell the user what to set up. Don't proceed without it.

### 2. Derive What This Project Needs

Do not apply a fixed list of building blocks. Read the strategy and derive what THIS project requires.

**From the EIID mapping, determine:**

- Does this product have users who log in? → it needs auth. Many products do, but a CLI tool or a cron pipeline doesn't.
- Does this product persist data? → it needs a schema. But a stateless webhook processor doesn't.
- Does this product have visual surfaces? → it needs a layout shell. But a WhatsApp-only or CLI product doesn't.
- Does this product have user-configurable behavior? → it needs settings. But a fixed-behavior tool doesn't.
- Does this product use LLM calls, workflows, or agents? → prompts must be discoverable and inspectable. The intelligence layer is not a black box.

**From the design system, determine the product's character:**

- What's the feel? The design direction determines how every surface is built — not just colors and fonts, but information density, interaction patterns, the rhythm of the experience.
- What's the signature? The one thing that makes this product recognizable. It should be present from the first build, not added later.
- What did the design explicitly reject? Don't build toward those defaults.

**Apply rule zero to the derivation itself.** Challenge every piece: does this product really need a settings page, or can preferences be inferred? Does it need a dashboard, or is the insight delivered conversationally? Does it need a database table for X, or is that data derived on the fly?

### 3. Plan

Produce the build plan. Each piece:

- **What:** concrete, tied to EIID
- **Why:** which user need it serves
- **How:** implementation level from the mapping (code/buy/LLM call/workflow/agent)
- **Depends on:** prerequisite pieces

Order by dependency. Infrastructure before intelligence. Intelligence before surfaces.

Present the plan. The user approves, reorders, removes, or adds. **This is the only checkpoint.** After approval, the build is autonomous until completion or hard failure.

### 4. Build Loop (autonomous)

For each piece in the approved plan:

Skills (eiid-awareness, design-awareness, build-awareness) fire during the loop. They are advisory — the build incorporates their feedback automatically and logs observations, but does not stop for them. Only hard failures (tests, types) stop the loop.

**Test → Build → Verify → Next.**

**Test:** write acceptance tests first. Tests define the outcome, not the implementation. What does this piece DO? Not how it's structured.

**Build:** implement. Follow the approach from the EIID mapping. For visual surfaces, build with the design system — tokens, typography, composition, the direction's character. Every visual element must earn its place. If a screen has 8 cards, ask: can it be 3? If a form has 10 fields, ask: which 4 actually matter?

**Verify:**
1. Acceptance tests pass
2. Full test suite passes (no regression)
3. Types check (`npx tsc --noEmit`)
4. If visual: does this follow the design direction? Not just token compliance — does it feel like the same product? Does the signature come through?

**On failure:** iterate on implementation, not tests. Maximum 5 iterations. If still failing, log what happened and skip to next piece. Continue building — don't stop the whole loop for one piece. Report all skipped pieces at the end.

**On success:** log to `.superskills/decisions.md` and continue.

### 5. Report

After all pieces:

```
## Build Report

Built: [count] pieces
Skipped: [count] (with reasons)
Tests: [pass]/[total]
EIID coverage: [which layers are implemented]

Skipped pieces:
- [piece]: [what failed, what was tried, proposed fix]
```

---

## Extend Mode

Add a feature or implement a piece on top of existing code.

### 1. Context

Read CLAUDE.md, `.superskills/decisions.md`, `.superskills/design-system.md`. Understand what exists.

### 2. Map to EIID

Which layer(s) does the target touch? What's the approach? What depends on it?

If the target doesn't trace to any EIID layer, flag it. Don't build scope creep silently.

### 3. Rule Zero

Before planning the implementation: is this the simplest way to deliver this value? Can the target be achieved with less? Fewer new screens, fewer new tables, fewer new endpoints. Challenge the scope before committing to it.

### 4. Build

Same loop as init mode (test → build → verify → next). Same autonomy — no checkpoints after initial confirmation.

### 5. Integration

Full test suite. Log to `decisions.md`. Report.

---

## Rules

- **Rule zero above all.** Build less. Every piece earns its place.
- **Tests define outcomes.** Write them first. They describe what the piece does, not how.
- **Autonomous after plan approval.** No checkpoints. Log, don't ask.
- **Five iterations max per piece.** Then skip and report. Don't block the whole build.
- **The strategy is truth.** If what you're building contradicts CLAUDE.md, stop and report. Don't deviate silently.
- **Design is not decoration.** The direction, the signature, the character — these are present in every piece, not applied at the end. A fleet dashboard feels like a control room from line one. A recipe app feels warm from line one.
- **Prompts are visible.** Every LLM prompt is discoverable, inspectable, and if user-facing, editable. No buried prompts in business logic.

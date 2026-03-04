---
description: Design system with craft. First run explores the product, chooses a direction, generates tokens. With a target screen, redesigns it.
allowed-tools: Read, Glob, Grep, Write, Edit, Bash, WebFetch, WebSearch
---

# Design

## Detect Mode

Two modes. Detection order matters.

**1. Does a documented design system exist?** Check for:
- `.superskills/design-system.md` (SuperSkills-managed, full context)
- `design-system.md` in project root
- `design-system/` directory with direction, tokens, and component docs inside

Framework config files alone (`components.json`, `tailwind.config.ts`, `globals.css`, `theme.ts`) do NOT count. Those mean a UI framework is installed, not that design decisions have been made.

**2. Route:**
- **No documented design system** → run **init mode**
- **Documented design system exists + user provided a target** (file path, screenshot, URL, or description of a specific screen or component) → run **redesign mode**
- **Documented design system exists + no target** → tell the user: "Design system already exists. Provide a target screen or component to redesign. Run `/review` for a full design audit."

---

## Init Mode

Read `reference/design-init-guide.md` for detailed execution of each step. Read `reference/design-craft.md` for craft principles. Read `reference/examples/design-system-saas.md` for tone and level of specificity.

### 1. Interface Modality Assessment

Follow the Interface Modality Assessment section in `reference/design-init-guide.md`. Read CLAUDE.md for the EIID mapping. For each EIID layer, determine whether it needs a visual surface, conversational delivery, notification, or embedded interface. Produce the EIID Interface Map table.

- If no layers need a visual surface: write the design system with EIID Interface Map, message structure patterns, and channel formatting only. Write to `.superskills/design-system.md`. Done. Skip all remaining steps.
- If some or all layers need a visual surface: proceed with the remaining steps, scoped to visual layers only.

### 2. Detect UI Framework

Only execute if the modality assessment identified layers requiring a visual surface.

Read package.json. Identify the UI framework:

| Framework | Detection |
|-----------|-----------|
| shadcn + Tailwind | `tailwindcss` + `components.json` or `@radix-ui/*` |
| Chakra UI | `@chakra-ui/react` |
| MUI | `@mui/material` |
| Mantine | `@mantine/core` |
| Tailwind only | `tailwindcss` without component library |
| None detected | no UI deps |

If none detected, ask the user. Suggest shadcn + Tailwind for new projects.

### 3. Explore the Product's World

Follow `reference/design-init-guide.md` — Explore section. Produce domain concepts, color world, signature, defaults to reject. Do not propose a direction until all four are produced.

### 4. Direction Assessment

Follow `reference/design-init-guide.md` — Direction Assessment section. Collect references (URLs, screenshots, Figma, brand names, designers, studios), anti-references, existing assets, constraints. Process all inputs and produce a reference summary.

### 5. Define Information Architecture

Before any visual decisions, define the structure. For layers mapped to a visual modality, define the information architecture. People feed data through their existing channels. The visual surface handles what those channels cannot:

1. **Visualizations** that don't fit in a message: charts, maps, timelines, trend comparisons, spatial views
2. **Configuration** (the control plane for the invisible layer): enrichment sources, inference prompts, delivery rules, user management, cron schedules

Read CLAUDE.md for the EIID mapping. Within visual scope, priority stack:
- **Interpretation** visualizations get the most screen space (trends, comparisons, spatial patterns)
- **Inference** outputs support them (alerts, scores, indicators)
- **Enrichment** configuration lives deep (set once, rarely touch)
- **Delivery** configuration is a settings concern (channels, triggers, timing)

Define: core objects (3-6), navigation budget (5-8 sidebar / 3-5 top bar), screen map with one focal point per screen, above the fold (2-4 elements), content depth tiers (surface / one click / deep). Each feature belongs to exactly one tier.

Do not proceed to style direction until IA is defined and confirmed.

### 6. Choose Style Direction

Follow `reference/design-init-guide.md` — Style Direction section. Three dimensions: density, shape, weight.

### 7. Define Typography Scale

Follow `reference/design-init-guide.md` — Typography Scale section. Brand fonts first. If none, pick display + body pairing that matches the direction. Then expand to full scale: all levels from Display through Mono with size, weight, line height, tracking, and usage.

### 8. Define Layout Architecture

Follow `reference/design-init-guide.md` — Layout Architecture section. Grid, breakpoints, container strategy, page patterns. Decisions flow from IA.

### 9. Define Composition Rules

Follow `reference/design-init-guide.md` — Composition Rules section. Hierarchy, density map, section rhythm, proportion, whitespace. These bridge layout and tokens.

### 10. Generate Token Layer

Follow `reference/design-init-guide.md` — Token Generation section. Extract before propose: scan existing code for repeated values, formalize the most common ones. Framework-specific instructions in the guide.

### 11. Write Design Configuration

Follow `reference/design-system-template.md` for structure. Read `reference/examples/design-system-saas.md` for tone.

Write to two places:

**CLAUDE.md** — Design System section (framework, style, token source, direction, navigation, typography, color character, signature).

**`.superskills/design-system.md`** — full design decisions: EIID Interface Map, direction, references, information architecture, layout, typography scale, composition, tokens, component patterns, decisions log. Include the EIID Interface Map section from step 1. For non-visual layers, write message structure and channel formatting patterns instead of visual tokens.

---

## Redesign Mode

Craft improvement on a single target, grounded in strategy. Consistency audits belong in review mode.

### 1. Load Design Context

Read the EIID Interface Map from `.superskills/design-system.md` (or extract from code if no documented design system exists). If the design system exists but has no EIID Interface Map section, skip modality-based scoping and proceed with visual redesign. If the redesign target belongs to a layer mapped to non-visual modality, focus the redesign on message structure and channel design rather than visual composition.

Read whatever design context exists, in priority order:

**If `.superskills/design-system.md` exists** (SuperSkills-managed): read it for direction, references, information architecture, layout, typography scale, composition, tokens, component patterns. Full context available.

**If not, extract from code:** scan globals.css / theme.ts / tailwind.config / components.json for the existing token set. Scan 5-10 component files for repeated values (spacing, colors, radius, heights). Build a working picture of the current design system from code evidence.

Read CLAUDE.md if it exists, for EIID mapping and user context. If no CLAUDE.md, work without strategic context and focus on craft.

Read `reference/design-critique.md` and `reference/design-craft.md`.

### 2. Identify Target

Read the target files. If a screenshot or image was provided, view it. If a URL was provided, fetch and analyze it. Establish the current state.

If the user pointed at a screenshot or URL without file paths, ask which source files implement that screen.

### 3. Intent Assessment

Show the user what you see (key observations about the current state). Then collect input in a single block. The user answers what they want, skips what they don't.

**What's wrong:** "What bothers you about this screen? Be specific if you can."

**What works:** "Anything to keep exactly as is?"

**References:** "Any references for this screen specifically? A site that does this type of screen well, a screenshot, a Figma file, a designer or studio whose approach fits."

For each reference provided:
- URLs: fetch the page, analyze the specific patterns relevant to this screen type
- Screenshots/images: view and extract applicable patterns
- Figma URLs: get design context, extract relevant patterns
- Designers or design studios: research their approach to this type of screen or problem. Apply their perspective, not just their aesthetic.

**Priority:** What matters most — visual identity, usability, density, craft, or structure?

Skip questions the user already answered in their initial request.

### 4. Critique

Apply the critique layers from `reference/design-critique.md` to the target screen. If CLAUDE.md with an EIID mapping exists, apply all six layers. If no strategic context, skip layer 0 and focus on layers 1-5.

Score each layer: **strong**, **adequate**, **weak**. Prioritize based on the user's stated priority.

### 5. Propose

The critique identified what's weak. Now apply `reference/design-craft.md` to generate improvements. Work through four craft dimensions for each weak or adequate layer.

**Spatial composition:** density variation between zones (dense for scanning, generous for focus), hierarchy through size + weight + space + position (three distinguishable tiers), section rhythm (three-number cadence: major sections / groups / items), proportion (focal element larger than supporting content), asymmetry and grid-breaking at focal points, negative space as grouping structure.

**Typography:** hierarchy works without color (size + weight alone produce three tiers), each level distinct in size + weight + color, fonts chosen intentionally (not framework defaults), monospace for aligned data.

**Surfaces and depth:** lightness shifts between levels (2-5%, not color jumps), consistent depth strategy (borders-only or subtle-shadows or layered, not mixed), opacity-based borders, complete interactive states on every clickable element.

**Identity:** direction spectrum pushed intentionally (density, temperature, energy, complexity), signature element visible, committed direction (not half-measures), anti-convergence test, atmosphere matching direction (or deliberate absence), every color with a documented job.

If the design system documents layout, typography scale, or composition rules, proposals use those values. Documented section rhythm of 48/24/12 means proposals use 48/24/12. Documented density map with a generous metrics zone means proposals give it space.

For each proposed change:

| Layer | Current | Proposed | Why |
|-------|---------|----------|-----|
| [layer] | [what exists now, specific] | [what to change, specific] | [craft principle from design-craft.md + design system constraint] |

Present the full proposal. Wait for user confirmation before implementing.

### 6. Implement

Apply approved changes. Use existing design tokens. Follow framework conventions:
- **shadcn + Tailwind:** semantic tokens only, `gap-*` for spacing, CVA for variants, `data-slot` attributes, search registries before building custom
- **Chakra/MUI/Mantine:** all changes through theme and framework APIs
- **Tailwind only:** utility classes, no arbitrary values

After implementation: update `.superskills/design-system.md` component patterns if new patterns were established. Log the decision to `.superskills/decisions.md`.

### Redesign Rules

- One screen at a time. No scope creep into adjacent screens.
- Propose before implementing. The user approves changes.
- Use existing tokens. Do not create new tokens without flagging it.
- If the redesign reveals a design system gap, fix it in the design system, not with a local workaround.
- Keep what works. Respect what the user said to preserve.
- Tie every change to strategy or craft. "This looks better" is not a rationale.

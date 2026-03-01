---
description: Design system with craft. First run explores the product, chooses a direction, generates tokens. With a target screen, redesigns it. After that, audits consistency, tokens, accessibility, and craft.
allowed-tools: Read, Glob, Grep, Write, Edit, Bash, WebFetch, WebSearch
---

# Design

## Detect Mode

Three modes. Detection order matters.

**1. Did the user provide a specific target?** A file path, screenshot, URL, or description of a specific screen or component. If yes, and a design system exists, run **redesign mode**. If yes but no design system exists, tell the user: "No design system found. Running init first to establish direction and tokens, then we can redesign the screen."

**2. Does a design system exist?** Check for any of these:
- `.superskills/design-system.md` (SuperSkills-managed, full context)
- `design-system.md` in project root
- `design-system/` directory (tokens, components, or docs inside)
- `design-tokens.json` or `tokens.json`
- `styles/tokens.css` or `styles/variables.css`
- `globals.css` or `global.css` with CSS custom properties
- `components.json` (shadcn config)
- `theme.ts` or `theme.js` or `theme/index.ts`
- `tailwind.config.ts` or `tailwind.config.js` with custom theme values

If a design system exists and no specific target was provided, run **review mode**. If no design system exists, run **init mode**.

---

## Init Mode

Read `reference/design-init-guide.md` for detailed execution of each step. Read `reference/design-craft.md` for craft principles. Read `reference/examples/design-system-saas.md` for tone and level of specificity.

### 1. Detect UI Framework

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

### 2. Explore the Product's World

Follow `reference/design-init-guide.md` — Explore section. Produce domain concepts, color world, signature, defaults to reject. Do not propose a direction until all four are produced.

### 3. Direction Assessment

Follow `reference/design-init-guide.md` — Direction Assessment section. Collect references (URLs, screenshots, Figma, brand names, designers, studios), anti-references, existing assets, constraints. Process all inputs and produce a reference summary.

### 4. Define Information Architecture

Before any visual decisions, define the structure. The web interface is not the primary input surface. People feed data through their existing channels. The interface serves two purposes:

1. **Visualizations** that don't fit in a message: charts, maps, timelines, trend comparisons, spatial views
2. **Configuration** — the control plane for the invisible layer: enrichment sources, inference prompts, delivery rules, user management, cron schedules

Read CLAUDE.md for the EIID mapping. Priority stack:
- **Interpretation** visualizations get the most screen space (trends, comparisons, spatial patterns)
- **Inference** outputs support them (alerts, scores, indicators)
- **Enrichment** configuration lives deep (set once, rarely touch)
- **Delivery** configuration is a settings concern (channels, triggers, timing)

Define: core objects (3-6), navigation budget (5-8 sidebar / 3-5 top bar), screen map with one focal point per screen, above the fold (2-4 elements), content depth tiers (surface / one click / deep). Each feature belongs to exactly one tier.

Do not proceed to style direction until IA is defined and confirmed.

### 5. Choose Style Direction

Follow `reference/design-init-guide.md` — Style Direction section. Three dimensions: density, shape, weight.

### 6. Choose Typography

Follow `reference/design-init-guide.md` — Typography section. Brand fonts first. If none, pick display + body pairing that matches the direction.

### 7. Generate Token Layer

Follow `reference/design-init-guide.md` — Token Generation section. Extract before propose: scan existing code for repeated values, formalize the most common ones. Framework-specific instructions in the guide.

### 8. Write Design Configuration

Follow `reference/design-system-template.md` for structure. Read `reference/examples/design-system-saas.md` for tone.

Write to two places:

**CLAUDE.md** — Design System section (framework, style, token source, direction, navigation, typography, color character, signature).

**`.superskills/design-system.md`** — full design decisions: direction, references, information architecture, tokens, component patterns, decisions log.

---

## Redesign Mode

Takes a specific screen or component and makes it better. Not a consistency audit (that's review mode). This is craft improvement on a single target, grounded in strategy.

### 1. Load Design Context

Read whatever design context exists, in priority order:

**If `.superskills/design-system.md` exists** (SuperSkills-managed): read it for direction, tokens, component patterns, references. Full context available.

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

For each layer scored "weak" or "adequate", propose specific changes:

| Layer | Current | Proposed | Why |
|-------|---------|----------|-----|
| [layer] | [what exists now, specific] | [what to change, specific] | [rationale tied to strategy or craft principle] |

Connect proposals to references and design system. Present the full proposal. Wait for user confirmation before implementing.

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

---

## Review Mode

Read CLAUDE.md and `.superskills/design-system.md` for context. Read `reference/design-critique.md` for the full critique framework. Work through all six layers (0 through 5).

### Accessibility (blocking)

WCAG 2.1 AA:
1. Color contrast: 4.5:1 normal text, 3:1 large text
2. Focus states on all interactive elements
3. Alt text on all images
4. Labels on all form inputs (not just placeholder)
5. Touch targets: 44x44px minimum
6. Semantic HTML: proper heading hierarchy, landmarks, ARIA
7. Keyboard navigation: all interactive elements reachable via tab
8. `cursor-pointer` on all clickable elements

### Information Architecture (blocking if widespread)

Read `.superskills/design-system.md` for the IA section. If no IA is documented, skip.

1. **Navigation budget:** compare actual nav items against documented budget
2. **Focal point:** compare actual visual weight against documented focal points
3. **Content depth:** check tier violations (enrichment config on surface, delivery outputs buried deep)
4. **Screen coverage:** compare implemented screens against documented screen map

### Cross-File Consistency (blocking if widespread)

Compare across all component files:
1. Type scale: same heading sizes and weights everywhere
2. Spacing rhythm: consistent spacing multiples across pages
3. Surface treatment: same card styles, border usage, shadow levels
4. Color usage: same semantic colors for same purposes

### Framework Rules

- **shadcn + Tailwind:** utility classes only, `gap-*` for containers, semantic tokens only, `data-slot` attributes, CVA for variants, spacing matches style preset
- **Chakra/MUI/Mantine:** all styling through framework APIs, theme overrides in theme file
- **Tailwind only:** utility classes only, no arbitrary values

### Craft (advisory)

Read `reference/design-critique.md` for the full critique framework. Read `reference/design-craft.md` for execution guidance. Apply all six critique layers. Flag craft issues as suggestions, not violations.

### Output

Write findings to `.superskills/report.md` — **replace** the Design Findings section. Update status counts.

| File:line | Rule | Issue | Fix |
|-----------|------|-------|-----|
| path:line | hard/N or framework/N or craft | description | fix |

If new component patterns were established during the review, append them to `.superskills/design-system.md`.

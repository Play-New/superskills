---
description: Design system with craft. First run explores the product, chooses a direction, generates tokens. After that, audits consistency, tokens, accessibility, and craft.
allowed-tools: Read, Glob, Grep, Write, Edit, Bash
---

# Design

## Detect Mode

Check for `.superskills/design-system.md` or globals.css with CSS custom properties or components.json. If a design system is configured, run **review mode**. Otherwise, run **init mode**.

---

## Init Mode

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

If none detected, ask the user. Suggest shadcn + Tailwind for new projects — base components share tokens, which guarantees consistency from day one.

### 2. Explore the Product's World

Read CLAUDE.md for business context. Before choosing any visual direction, explore:

**Who opens this interface?** Not "users" — the actual person. Where are they? What's on their mind? A teacher at 7am with coffee is not a developer debugging at midnight.

**What must they accomplish?** The verb. Grade submissions. Find the broken deployment. Approve the payment.

**What should this feel like?** Say it in words that mean something. "Clean and modern" means nothing. Warm like a notebook? Cold like a terminal? Dense like a trading floor? Calm like a reading app?

Then produce:

- **Domain concepts:** 5+ metaphors and vocabulary from this product's world. Not features — territory.
- **Color world:** 5+ colors that exist naturally in this product's domain. If this were a physical space, what would you see?
- **Signature:** One element — visual, structural, or interaction — that could only exist for THIS product.
- **Defaults to reject:** 3 obvious choices you will NOT use. Name them explicitly so you can avoid them.

Also consider:

- **Spatial composition:** asymmetry, overlap, density variation, grid-breaking as intentional choices. Not every layout sits on a centered 12-column grid.
- **Atmosphere:** backgrounds are not solid colors. Gradient, grain, texture that matches the direction. A reading app's warm paper texture is not a trading floor's dark matte surface.
- **Anti-convergence:** if another AI given similar prompts would produce substantially identical output, the design has failed. What makes this product visually distinct from a default template?

Do not propose a direction until all four items above are produced.

### 3. Define Information Architecture

Before any visual decisions, define the structure of the product. Screen hierarchy determines density. Density determines typography and spacing. Skipping this step produces interfaces where everything has equal weight.

Read CLAUDE.md for the EIID mapping. The four layers determine what the product shows and where:

- **Delivery** outputs (channels, triggers, timing) define the primary screens. These are where the user spends time.
- **Interpretation** outputs (insights, framing) define the focal points. The most valuable content on each screen comes from this layer.
- **Inference** outputs (detections, predictions, anomalies) feed into interpretation. They surface as alerts, scores, trend indicators.
- **Enrichment** configuration (data sources, connections) lives deep. Users set it up once, then rarely touch it.

This is the priority stack: delivery screens get the most attention, enrichment config gets the least.

**a) Core objects.** Derive from the EIID mapping. Each delivery channel or interpretation output suggests an object the user cares about. A fleet app with "vehicles needing attention" in interpretation and "WhatsApp alerts" in delivery produces objects like Vehicles, Alerts, Routes, Reports. Not features, not pages. The nouns of the product. Each object typically maps to a nav destination and at least one screen (index + detail).

**b) Navigation budget.** Choose type (sidebar, top bar, tab bar, hybrid) and populate with items derived from the objects. Budget: 5-8 items for sidebar, 3-5 for top bar. Everything that doesn't fit lives inside a screen, behind a menu, or in settings. Document what was excluded and where it went. Labels must be specific to this product. Test: if a label could belong to any product, it's too generic.

**c) Screen map with focal points.** For each primary screen, name ONE element that gets the most visual weight. Focal points should come from interpretation or inference outputs, not from enrichment or configuration. Not "the dashboard" but "the active alerts count." If a screen has no single focal point, it either needs splitting or prioritizing. Include drill-down screens (Vehicle Detail) that aren't nav items but are primary interaction targets.

**d) Above the fold.** For each screen, 2-4 elements visible without scrolling. This is the attention budget. Interpretation outputs (actionable insights) and inference outputs (live indicators) go above the fold. Enrichment status and configuration go below. Adding something above the fold means naming what moves below it.

**e) Content depth.** Three tiers mapped to EIID layers and task frequency:
- Surface (multiple times daily): delivery screens, interpretation insights, inference alerts. Visible without interaction.
- One click (daily/weekly): inference detail (trend charts, history, comparisons that explain the surface). Behind a tab, drawer, or expand.
- Deep (monthly/rarely): enrichment configuration (data sources, connections), delivery channel setup (triggers, schedules). Behind navigation, search, or settings.

Each feature, metric, or action belongs to exactly one tier. No tier overflow: if surface gets crowded, something moves to one-click.

Do not proceed to style direction until IA is defined and confirmed. Screen structure determines density, which determines typography and spacing.

### 4. Choose Style Direction

For shadcn projects, run `npx shadcn@latest init` to see available styles. Pick the one that matches the product's character from step 2. The style determines spacing rhythm, border radius, and component density. Present the recommendation to the user with reasoning.

Three dimensions to decide:
- **Density:** tight (data-heavy, admin) vs. generous (consumer, editorial)
- **Shape:** sharp/boxy (technical) vs. rounded/pill (approachable)
- **Weight:** compact (efficiency-first) vs. spacious (comfort-first)

For non-shadcn projects, skip this step — the equivalent decisions are encoded directly in the theme file.

### 5. Choose Typography

Pick a display font + body font pairing that matches the direction. Ask the user if they have brand fonts first. If they do, use those.

If not, search for fonts that match the direction. Browse foundries (Google Fonts, Fontshare, Atipo) for options. Choose based on the product's character, not popularity.

**The default test:** if the font is what the framework installs by default, or what most AI-generated projects use, it's not a design decision. Choose intentionally. A fleet management dashboard and a children's reading app should not share the same typeface.

Pick two: one display font (headings, hero text) and one body font (paragraphs, UI labels). They should contrast in character but share similar x-height.

### 6. Generate Token Layer

All aesthetic choices become design tokens. No creative decision lives in component code.

**Extract before propose:** if existing UI code is present, discover the system already embedded in the code before proposing anything new.

1. Scan all .tsx/.jsx/.vue/.svelte files for repeated CSS values: padding, margin, gap, border-radius, height, font-size, color, box-shadow
2. Count occurrences. Group by property. Sort by frequency.
3. Present findings: "36px height used in 7 of 8 buttons. 16px padding in 12 components. border-radius 8px in 9 places, 4px in 3."
4. Propose a token set that formalizes the most common values. Flag outliers as candidates for consolidation.
5. Ask the user to confirm before writing tokens. The existing code is evidence. Respect it.

If no existing UI code is found, skip extraction and propose tokens from the direction.

**For shadcn + Tailwind:**
- Run `npx shadcn@latest create` with the chosen style, or generate `globals.css` manually
- CSS custom properties in OKLCH (Tailwind v4) or HSL. Both `:root` and `.dark`
- Variables for: colors (background, foreground, card, primary, secondary, accent, muted, destructive, border, ring), `--radius`, `--font-display`, `--font-body`, spacing scale, shadow scale, transition durations
- Token names should evoke the product's world — not just `--gray-700` but names that someone reading only the tokens could guess what product this is
- Set up cn() utility: `npm install clsx tailwind-merge`
- Initialize: `npx shadcn init`
- Add base components: `npx shadcn add button card input label badge dialog dropdown-menu table`

**For Chakra/MUI/Mantine:**
- Create theme.ts with full token set: colors, fonts (display + body), spacing, radii, shadows
- Wrap app in the framework's provider

**For Tailwind only:**
- Create globals.css with the same CSS variable set
- Configure tailwind.config.ts extending from variables

**Key rule:** the token file IS the aesthetic. Strip all components and rebuild from tokens alone — the product should still look the same.

### 7. Write Design Configuration

Read `reference/examples/design-system-saas.md` for tone and structure. Match that level of specificity — concrete values, rationale for each decision, measurement table populated from the start.

Write to two places:

**CLAUDE.md** — stable instructions Claude reads at session start:

```
## Design System
**Framework:** [detected]
**Style:** [chosen style or n/a]
**Token source:** [globals.css / theme.ts / etc.]
**Direction:** [one sentence: "dense and authoritative — tight spacing, monospace accents, slate palette with amber data highlights"]
**Navigation:** [type] — [nav items]
**Typography:** [display font] + [body font]
**Color character:** [what the palette feels like]
**Signature:** [the one element unique to this product]
```

**`.superskills/design-system.md`** — detailed design decisions for enforcement:

```
# Design System

## Direction
**Feel:** [intent statement]
**Domain concepts:** [5+ from exploration]
**Rejected defaults:** [3 things you chose NOT to do]

## Information Architecture
**Navigation:** [type] — [items, 5-8 max]
**Excluded from nav:** [what was cut, where it lives instead]

| Screen | Focal Point | Above the Fold | One Click | Deep |
|--------|-------------|----------------|-----------|------|
| [screen] | [one element] | [2-4 items] | [behind tab/drawer] | [behind nav/search] |

**Content depth rules:**
- Surface: [what qualifies — tied to task frequency]
- One click: [what qualifies]
- Deep: [what qualifies]

## Tokens
**Spacing base:** [4px]
**Scale:** [4, 8, 12, 16, 24, 32]
**Radius:** [per style preset]
**Depth strategy:** [borders-only / subtle-shadows / layered]
**Color primitives:** [foreground levels, surfaces, accent, semantic]

## Component Patterns

| Component | Height | Padding | Radius | Notes |
|-----------|--------|---------|--------|-------|
| [populated as components are built] | | | | |

## Decisions
| Decision | Why | Date |
|----------|-----|------|
| [first decision from init] | [rationale] | [date] |
```

---

## Review Mode

Read CLAUDE.md and `.superskills/design-system.md` for context. Token compliance and component discovery are handled by the hook and skill respectively. Review focuses on what automation cannot catch.

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

### Cross-File Consistency (blocking if widespread)

Compare across all component files:
1. **Type scale:** same heading sizes, body text sizes, and weights everywhere
2. **Spacing rhythm:** consistent spacing multiples across pages (not 16px gaps on one page and 20px on another)
3. **Surface treatment:** same card styles, same border usage, same shadow levels
4. **Color usage:** same semantic colors for same purposes (not primary blue for CTAs on one page and accent teal on another)

### Framework Rules

**shadcn + Tailwind:**
1. Tailwind utility classes only, no custom CSS classes
2. `gap-*` for flex/grid containers, not `space-y-*` or margins
3. Semantic color tokens only (`text-foreground`, `bg-muted`), not Tailwind colors (`text-neutral-500`)
4. `data-slot` attributes on component wrappers
5. CVA for component variants
6. Spacing values match the style preset

**Chakra/MUI/Mantine:**
1. All styling through framework APIs, no inline CSS
2. Theme overrides in theme file, not per-component

**Tailwind only:**
1. Utility classes only, no arbitrary values

### Craft (advisory)

Read `reference/design-critique.md` for the full critique framework. Read `reference/design-craft.md` for execution guidance. Apply the four critique layers:

**Composition:** does the layout have rhythm, proportion, and focal points? Or is spacing uniform and everything equal weight?

**Craft:** are surfaces subtly layered (2-5% lightness shifts, low-opacity borders)? Do all interactive elements have hover, focus, active, disabled states? Is typography hierarchy visible without color?

**Content:** does placeholder data feel real? Do labels and headings speak the same voice? Are empty states designed?

**Structure:** are there CSS workarounds (negative margins, calc for basic spacing, absolute positioning for layout, !important, magic numbers)? These signal incomplete structure.

Then run these identity tests:

**The name test:** describe the UI to someone who cannot see it. If it sounds generic, the design lacks identity.

**The swap test:** swap the typeface for a default. Swap the layout for a template. Where it wouldn't matter = where you defaulted.

Flag craft issues as suggestions, not violations.

### Output

Write findings to `.superskills/report.md` — **replace** the Design Findings section. Update status counts.

| File:line | Rule | Issue | Fix |
|-----------|------|-------|-----|
| path:line | hard/N or framework/N or craft | description | fix |

If new component patterns were established during the review, append them to `.superskills/design-system.md`.

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
- `globals.css` or `global.css` with CSS custom properties
- `components.json` (shadcn config)
- `theme.ts` or `theme.js` or `theme/index.ts`
- `tailwind.config.ts` or `tailwind.config.js` with custom theme values

If a design system exists and no specific target was provided, run **review mode**. If no design system exists, run **init mode**.

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

Read CLAUDE.md for business context and EIID mapping. Before choosing any visual direction, explore:

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

### 3. Direction Assessment

Present findings from step 2 to the user. Then collect input in a single block. The user answers what they want, skips what they don't.

**References:**
"Any visual references? URLs of sites you admire, screenshots, Figma files, brand names. Even from different domains, as long as something about them feels right for this product."

For each reference provided:
- URLs: fetch the page, analyze layout density, palette, typography, spacing rhythm, atmosphere, navigation pattern
- Screenshots/images: view the image, same analysis
- Figma URLs: get design context via Figma tools, same analysis
- Brand names: search for the brand's visual identity, then analyze

**Anti-references:**
"Anything you've seen and actively don't want? Sites, styles, or patterns that would be wrong here."

**Existing assets:**
"Any brand material? Logo, colors, fonts, illustration style, tone guide. Even a rough sketch."

For existing assets: analyze and incorporate as constraints. Brand colors become the palette foundation. Brand fonts skip the typography selection step. A logo's character informs the direction.

**Direction check:**
"Step 2 produced this direction: [summary]. Does this match? Anything to push further, pull back, or change entirely?"

**Constraints:**
"Hard constraints? Required colors, mandated fonts, accessibility targets beyond AA, platform requirements."

Process all inputs. For each reference and anti-reference, extract concrete patterns:
- What to borrow: specific and named ("the tight sidebar density from Linear", "the amber-on-dark palette from that dashboard screenshot")
- What to avoid: specific and named ("the generic card grid from that SaaS site", "the low-contrast gray text")

Produce a reference summary: 3-7 distilled patterns across all references. Not "modern and clean" but "tight 8px grid, monochrome surfaces, single accent color for CTAs, generous whitespace between sections."

Revise the direction from step 2 based on assessment answers. If the user provided nothing, proceed with step 2's direction as-is.

Store the reference summary in `.superskills/design-system.md` under a References section.

### 4. Define Information Architecture

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

### 5. Choose Style Direction

For shadcn projects, run `npx shadcn@latest init` to see available styles. Pick the one that matches the product's character from step 2. The style determines spacing rhythm, border radius, and component density. Present the recommendation to the user with reasoning.

Three dimensions to decide:
- **Density:** tight (data-heavy, admin) vs. generous (consumer, editorial)
- **Shape:** sharp/boxy (technical) vs. rounded/pill (approachable)
- **Weight:** compact (efficiency-first) vs. spacious (comfort-first)

For non-shadcn projects, skip this step — the equivalent decisions are encoded directly in the theme file.

### 6. Choose Typography

Ask the user if they have brand fonts first. If they do, use those.

If not, pick a display font + body font pairing that matches the direction. Read `reference/design-craft.md` for the Typography Character section. Search for fonts that match the direction. Browse foundries (Google Fonts, Fontshare, Atipo, fonts.bunny.net) for options. Choose based on the product's character, not popularity.

**The default test:** if the font is what the framework installs by default, or what most AI-generated projects use, it's not a design decision. Choose intentionally. A fleet management dashboard and a children's reading app should not share the same typeface.

Pick two: one display font (headings, hero text) and one body font (paragraphs, UI labels). They should contrast in character but share similar x-height.

If references from step 3 included typography patterns, use them as starting points.

### 7. Generate Token Layer

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

### 8. Write Design Configuration

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

## References
**Borrowed:** [specific patterns from references: "tight sidebar density from Linear", "amber-on-dark from the dashboard screenshot"]
**Avoided:** [specific anti-patterns: "generic card grid", "low-contrast body text"]
**Assets:** [brand materials incorporated: "logo uses geometric sans, informed display font choice"]

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

## Redesign Mode

Takes a specific screen or component and makes it better. Not a consistency audit (that's review mode). This is craft improvement on a single target, grounded in strategy.

### 1. Load Design Context

Read whatever design context exists, in priority order:

**If `.superskills/design-system.md` exists** (SuperSkills-managed): read it for direction, tokens, component patterns, references. Full context available.

**If not, extract from code:** scan globals.css / theme.ts / tailwind.config / components.json for the existing token set. Scan 5-10 component files for repeated values (spacing, colors, radius, heights). Build a working picture of the current design system from code evidence. This is the same "extract before propose" logic from init step 7, applied here.

Read CLAUDE.md if it exists, for EIID mapping and user context. If no CLAUDE.md, work without strategic context and focus on craft.

Read `reference/design-critique.md` and `reference/design-craft.md`.

### 2. Identify Target

Read the target files. If a screenshot or image was provided, view it. If a URL was provided, fetch and analyze it. Establish the current state.

If the user pointed at a screenshot or URL without file paths, ask which source files implement that screen.

### 3. Intent Assessment

Show the user what you see (key observations about the current state). Then collect input in a single block. The user answers what they want, skips what they don't.

**What's wrong:**
"What bothers you about this screen? Be specific if you can: too dense, too generic, confusing hierarchy, feels unfinished, doesn't match the brand."

**What works:**
"Anything to keep exactly as is? Elements, patterns, layout choices that are right."

**References:**
"Any references for this screen specifically? A site that does this type of screen well, a screenshot, a Figma file."

For each reference provided:
- URLs: fetch the page, analyze the specific patterns relevant to this screen type (not the whole site)
- Screenshots/images: view and extract applicable patterns
- Figma URLs: get design context, extract relevant patterns

**Priority:**
"What matters most for this redesign?"
- Visual identity: look more like the product's world, less like a template
- Usability: easier to navigate, clearer hierarchy, faster to scan
- Density: show more information without feeling crowded, or reduce clutter
- Craft: polish, states, micro-interactions, surface treatment
- Structure: reorganize content, fix layout problems, improve flow

Skip questions the user already answered in their initial request. If they said "this dashboard looks like every other SaaS", that's "what's wrong" (generic) and "priority" (visual identity) already answered.

### 4. Critique

Apply the critique layers from `reference/design-critique.md` to the target screen. If CLAUDE.md with an EIID mapping exists, apply all six layers. If no strategic context, skip layer 0 and focus on layers 1-5.

**Layer 0 — Strategic alignment** (requires EIID mapping):
Does the screen serve the right EIID layer? Is the focal point aligned with the highest-value content? Does the density and tone match the user described in CLAUDE.md?

**Layer 1 — Composition:**
Does the layout have rhythm and hierarchy? Is there one clear focal point? Is spacing varied intentionally? Does spatial composition support the information architecture?

**Layer 2 — Craft:**
Surface layering, border treatment, interactive states, typography depth. Read `reference/design-craft.md` for the full range: subtle layering, atmosphere, spatial composition, typography character.

**Layer 3 — Content:**
Placeholder quality, voice consistency, empty states, error messages.

**Layer 4 — Structure:**
CSS smells: negative margins, calc for spacing, absolute positioning for layout, !important, magic numbers.

**Layer 5 — Identity:**
Run the four tests: name test (can you describe it specifically?), swap test (what's generic?), convergence test (would another AI produce the same?), screenshot test (recognizable without the logo?).

Score each layer: **strong** (no issues), **adequate** (minor issues), **weak** (significant issues).

Prioritize based on the user's stated priority from step 3. If they said "visual identity", weight layers 0 and 5 heavily. If they said "craft", weight layer 2. If they said "structure", weight layer 4.

### 5. Propose

For each layer scored "weak" or "adequate", propose specific changes:

| Layer | Current | Proposed | Why |
|-------|---------|----------|-----|
| [layer] | [what exists now, specific] | [what to change, specific] | [rationale tied to strategy or craft principle] |

If references were collected in step 3, explicitly connect proposals to reference patterns: "Borrowing the tight data density from [reference name]."

Connect each proposal to the design system:
- Which tokens apply?
- Which component patterns to reuse?
- Any new patterns this redesign establishes?

Present the full proposal. Wait for user confirmation before implementing. The user may approve all, reject some, or adjust priorities.

### 6. Implement

Apply approved changes. Use existing design tokens. Follow framework rules:

**shadcn + Tailwind:**
- Semantic color tokens only (`text-foreground`, `bg-muted`), not raw colors
- `gap-*` for spacing in flex/grid, not margins
- CVA for component variants if adding new variant styles
- `data-slot` attributes on wrappers
- Before building custom components, search shadcn registries (base, community: @reui, @animate-ui, @diceui and others) for existing implementations

**Chakra/MUI/Mantine:**
- All changes through theme and framework APIs
- No inline styles or custom CSS

**Tailwind only:**
- Utility classes, no arbitrary values

After implementation:

1. **Update measurements:** if new component patterns were established (new heights, padding values, radius), append them to `.superskills/design-system.md` Component Patterns table. If no `.superskills/design-system.md` exists, suggest running init to formalize the design system.
2. **Log the decision:** if `.superskills/decisions.md` exists, append:
   ```
   ### [date] - Redesign: [screen name]
   **Type:** redesign
   **Summary:** [what changed and why, 1-2 sentences]
   **Layers addressed:** [which critique layers were improved]
   **References used:** [if any]
   ```
3. **Show the result:** describe what changed, layer by layer. If a screenshot was provided as input, suggest the user compare before and after.

### Redesign Rules

- One screen at a time. Do not scope-creep into adjacent screens.
- Propose before implementing. The user approves changes.
- Use existing tokens. Do not create new tokens without flagging it.
- If the redesign reveals a design system gap (missing token, undocumented pattern), flag it but fix it in the design system, not with a local workaround.
- Keep what works. The user said what to preserve in step 3. Respect it.
- Tie every change to strategy or craft. No changes for aesthetic preference alone. "This looks better" is not a rationale. "The focal point now matches the highest-value EIID output" is.

---

## Review Mode

Read CLAUDE.md and `.superskills/design-system.md` for context. Token compliance and component discovery are handled by the hook and skill respectively. Review focuses on what automation cannot catch.

Read `reference/design-critique.md` for the full critique framework. Work through all six layers (0 through 5).

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

1. **Navigation budget:** count navigation items in the actual code (sidebar, top bar, tab bar). Compare against the documented budget. Flag items that exist in code but not in the documented nav, and documented items missing from code.
2. **Focal point:** for each primary screen, identify the element with the most visual weight (size, contrast, position). Compare against the documented focal point. If a different element dominates, flag it.
3. **Content depth:** check that elements are at the correct tier. Surface items (documented as "multiple times daily") should be visible without interaction. One-click items should be behind a tab, drawer, or expand. Deep items should be behind navigation or settings. Flag tier violations: enrichment config visible on the surface, or delivery outputs buried deep.
4. **Screen coverage:** compare implemented screens against the documented screen map. Flag undocumented screens (code exists, no IA entry) and unimplemented screens (IA entry exists, no code).

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

Read `reference/design-critique.md` for the full critique framework. Read `reference/design-craft.md` for execution guidance. Apply all five critique layers (0 through 4), then run the identity tests from layer 5.

Flag craft issues as suggestions, not violations.

### Output

Write findings to `.superskills/report.md` — **replace** the Design Findings section. Update status counts.

| File:line | Rule | Issue | Fix |
|-----------|------|-------|-----|
| path:line | hard/N or framework/N or craft | description | fix |

If new component patterns were established during the review, append them to `.superskills/design-system.md`.

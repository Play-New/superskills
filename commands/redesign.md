---
description: Redesign a screen or component. Analyze current state, apply strategic critique, incorporate references, propose and implement improvements.
allowed-tools: Read, Glob, Grep, Write, Edit, Bash, WebFetch, WebSearch
---

# Redesign

Takes an existing screen or component and makes it better. Not a consistency audit (that's `/super:design` review mode). This is craft improvement on a specific target, grounded in strategy.

Requires a design system. If `.superskills/design-system.md` does not exist, stop and tell the user to run `/super:design` first.

---

## 1. Identify Target

Ask the user: "Which screen or component? Provide file path(s), a screenshot, or describe it."

Read the target files. If a screenshot or image is provided, view it. If a URL is provided, fetch and analyze it. Establish the current state.

If the user pointed at a screenshot or URL without file paths, ask which source files implement that screen.

## 2. Intent Assessment

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

## 3. Load Context

Read all of:
- CLAUDE.md for EIID mapping, user context, business context
- `.superskills/design-system.md` for direction, tokens, component patterns, references
- `reference/design-critique.md` for the critique framework
- `reference/design-craft.md` for execution guidance

Map the target screen to the EIID layers:
- Which EIID layer does this screen primarily serve? (delivery, interpretation, inference, enrichment)
- What should the focal point be based on the EIID mapping?
- What content depth tier are the elements on this screen?

## 4. Critique

Apply all five critique layers from `reference/design-critique.md` to the target screen:

**Layer 0 — Strategic alignment:**
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

Prioritize based on the user's stated priority from step 2. If they said "visual identity", weight layers 0 and 5 heavily. If they said "craft", weight layer 2. If they said "structure", weight layer 4.

## 5. Propose

For each layer scored "weak" or "adequate", propose specific changes:

| Layer | Current | Proposed | Why |
|-------|---------|----------|-----|
| [layer] | [what exists now, specific] | [what to change, specific] | [rationale tied to strategy or craft principle] |

If references were collected in step 2, explicitly connect proposals to reference patterns: "Borrowing the tight data density from [reference name]."

If `.superskills/design-system.md` has a References section from `/super:design`, check those patterns too.

Connect each proposal to the design system:
- Which tokens apply?
- Which component patterns to reuse?
- Any new patterns this redesign establishes?

Present the full proposal. Wait for user confirmation before implementing. The user may approve all, reject some, or adjust priorities.

## 6. Implement

Apply approved changes. Use design tokens from `.superskills/design-system.md`. Follow framework rules:

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

1. **Update measurements:** if new component patterns were established (new heights, padding values, radius), append them to `.superskills/design-system.md` Component Patterns table.
2. **Log the decision:** append to `.superskills/decisions.md`:
   ```
   ### [date] - Redesign: [screen name]
   **Type:** redesign
   **Summary:** [what changed and why, 1-2 sentences]
   **Layers addressed:** [which critique layers were improved]
   **References used:** [if any]
   ```
3. **Show the result:** describe what changed, layer by layer. If a screenshot was provided as input, suggest the user compare before and after.

---

## Rules

- One screen at a time. Do not scope-creep into adjacent screens.
- Propose before implementing. The user approves changes.
- Use existing tokens. Do not create new tokens without flagging it.
- If the redesign reveals a design system gap (missing token, undocumented pattern), flag it but fix it in the design system, not with a local workaround.
- Keep what works. The user said what to preserve in step 2. Respect it.
- Tie every change to strategy or craft. No changes for aesthetic preference alone. "This looks better" is not a rationale. "The focal point now matches the highest-value EIID output" is.

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

If none detected, ask the user. Suggest shadcn + Tailwind for new projects.

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

Do not propose a direction until all four are produced.

### 3. Choose Style Preset

For shadcn projects, pick a style that matches the product:

| Style | Character | Spacing | Shape | For |
|-------|-----------|---------|-------|-----|
| **Vega** | Classic | Standard | Medium radius | General purpose |
| **Nova** | Compact | Tight | Standard | Data-heavy dashboards |
| **Maia** | Soft | Generous | Rounded/pill | Consumer apps |
| **Lyra** | Sharp | Standard | Boxy/none | Technical tools |
| **Mira** | Dense | Very tight | Compact | Admin interfaces |

Present the recommendation to the user with reasoning. The style determines spacing rhythm, border radius, and component density throughout the project.

For non-shadcn projects, skip this step — the equivalent decisions are encoded directly in the theme file.

### 4. Choose Typography

Pick a display font + body font pairing that matches the direction. Never fall back to generic fonts.

**Banned:** Inter, Roboto, Arial, system-ui as primary choices. These are defaults, not decisions.

**Good pairings by direction:**
- Technical/precise: monospace display (JetBrains Mono, IBM Plex Mono) + clean body (Geist, Satoshi)
- Warm/approachable: rounded display (Nunito, Quicksand) + readable body (Source Sans, Lato)
- Editorial/authoritative: serif display (Fraunces, Playfair Display) + neutral body (DM Sans, Outfit)
- Bold/modern: geometric display (Clash Display, Cabinet Grotesk) + refined body (Satoshi, General Sans)

Ask the user if they have brand fonts. If they do, use those. If not, choose from the direction.

### 5. Generate Token Layer

All aesthetic choices become design tokens. No creative decision lives in component code.

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

### 6. Write Design Configuration

Write to two places:

**CLAUDE.md** — stable instructions Claude reads at session start:

```
## Design System
**Framework:** [detected]
**Style:** [Vega/Nova/Maia/Lyra/Mira or n/a]
**Token source:** [globals.css / theme.ts / etc.]
**Direction:** [one sentence: "dense and authoritative — tight spacing, monospace accents, slate palette with amber data highlights"]
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

## Tokens
**Spacing base:** [4px]
**Scale:** [4, 8, 12, 16, 24, 32]
**Radius:** [per style preset]
**Depth strategy:** [borders-only / subtle-shadows / layered]
**Color primitives:** [foreground levels, surfaces, accent, semantic]

## Component Patterns
[populated as components are built — spacing, heights, padding patterns]

## Decisions
| Decision | Why | Date |
|----------|-----|------|
| [first decision from init] | [rationale] | [date] |
```

---

## Review Mode

Read CLAUDE.md and `.superskills/design-system.md` for context. Three categories of checks.

### Hard Rules (blocking)

**Accessibility (WCAG 2.1 AA):**
1. Color contrast: 4.5:1 normal text, 3:1 large text
2. Focus states on all interactive elements
3. Alt text on all images
4. Labels on all form inputs (not just placeholder)
5. Touch targets: 44x44px minimum
6. Semantic HTML: proper heading hierarchy, landmarks, ARIA
7. Keyboard navigation: all interactive elements reachable via tab

**Token compliance:**
8. No hardcoded color values (hex, rgb, hsl, oklch) in component files — all from tokens
9. No font-family declarations in components — all from tokens
10. No one-off shadow, border-radius, or transition values — use the token scale
11. No inline styles that override the token system
12. No arbitrary Tailwind values (`-[...]` syntax) — if you need a value, add it to the token scale

**Interaction:**
13. `cursor-pointer` on all clickable elements
14. No Unicode escape sequences for accented characters — use UTF-8 directly

**Responsive:**
15. Layout works at 320px minimum
16. Consistent breakpoint usage

### Framework-Specific Rules

**shadcn + Tailwind:**
1. Search existing components across registries before building custom. Check @shadcn (440+ components), @reui (700+), @animate-ui (200+), @diceui (100+), @blocks, and community registries. Install via `npx shadcn@latest add @registry/component-name`.
2. Use shadcn base components before custom implementations
3. Tailwind utility classes only — no custom CSS classes
4. Use `gap-*` for flex/grid containers — not `space-y-*` or margins
5. Semantic color tokens only (`text-foreground`, `bg-muted`) — not Tailwind colors (`text-neutral-500`, `bg-gray-100`)
6. Use `data-slot` attributes on component wrappers
7. Use CVA (Class Variance Authority) for component variants
8. Spacing values must match the style preset (Maia=generous, Nova/Mira=compact)

**Chakra/MUI/Mantine:**
1. Use framework components before custom implementations
2. All styling through framework APIs — no inline CSS
3. Theme overrides in theme file, not per-component

**Tailwind only:**
1. Utility classes only — no custom CSS classes
2. No arbitrary values

### Craft Checks (advisory)

Run these against the output before presenting:

**The swap test:** If you swapped the typeface for a generic one, would anyone notice? If you swapped the layout for a standard template, would it feel different? Where swapping wouldn't matter = where you defaulted.

**The squint test:** Blur your eyes. Can you still perceive hierarchy? Is anything jumping out harshly?

**The token test:** Read the CSS variables. Do they sound like they belong to this product, or could they belong to any project?

**Cross-page cohesion:** Do all pages feel like the same product? Same type scale, same spacing, same color usage, same surface treatment. If page A uses generous whitespace and page B is cramped, flag it.

**Motion:** Are transitions present where they improve UX (page transitions, loading states, hover feedback)? Are they consistent? Use 150ms for hover/focus, 200ms for color transitions, 300ms for modal enter/exit.

Flag craft issues as suggestions, not violations.

### Output

Write findings to `.superskills/report.md` — **replace** the Design Findings section. Update status counts.

| File:line | Rule | Issue | Fix |
|-----------|------|-------|-----|
| path:line | hard/N or framework/N or craft | description | fix |

If new component patterns were established during the review, append them to `.superskills/design-system.md`.

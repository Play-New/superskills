---
description: Design system. First run detects framework and sets up tokens. After that, framework-adaptive audit + universal accessibility rules.
allowed-tools: Read, Glob, Grep, Write, Edit, Bash
---

# Design

## Detect Mode

Check for globals.css with CSS custom properties OR components.json OR a UI framework in package.json. If a design system is already configured, run **review mode**. Otherwise, run **init mode**.

---

## Init Mode

### 1. Detect UI Framework

Read package.json. Identify which UI framework is installed:

| Framework | Detection |
|-----------|-----------|
| shadcn + Tailwind | `tailwindcss` + `components.json` or `@radix-ui/*` |
| Chakra UI | `@chakra-ui/react` |
| MUI | `@mui/material` |
| Mantine | `@mantine/core` |
| Ant Design | `antd` |
| Tailwind only | `tailwindcss` without component library |
| None detected | no UI deps |

If none detected, ask the user what they want. Suggest shadcn + Tailwind as default for new projects but do not impose it.

### 2. Choose Aesthetic Direction

The design system is not just a toolkit — it's the product's personality. Before setting up tokens, decide what that personality is.

Read CLAUDE.md for business context (who the user is, what the product does). Then ask the user:

**What should this feel like?** Give 3-4 concrete directions to choose from, inferred from the product context. Examples:

- A fintech dashboard → "clean and authoritative" (tight spacing, monospace accents, muted palette with sharp data colors)
- A creative tool → "expressive and spatial" (generous whitespace, bold type scale, warm palette, playful motion)
- An ops platform → "dense and functional" (compact layout, high information density, neutral palette, minimal decoration)
- A consumer app → "soft and inviting" (rounded corners, friendly type, pastel palette, smooth transitions)

Each direction should specify: **typography pairing** (display + body), **color character** (not just "primary/secondary" — what feeling), **spatial rhythm** (tight/generous/mixed), **surface treatment** (flat/layered/textured), **motion approach** (minimal/smooth/playful).

The user picks one. If they have brand assets (colors, fonts, logo), incorporate them. If not, make distinctive choices — never fall back to default shadcn gray or generic fonts (Inter, Roboto, Arial).

### 3. Encode Direction as Tokens

All aesthetic choices become design tokens. No creative decision should live in component code — it all goes in the token layer.

**For shadcn + Tailwind:**
- Create globals.css with CSS custom properties in HSL. Both `:root` (light) and `.dark` (dark).
- Include variables for: colors (background, foreground, card, primary, secondary, accent, muted, destructive, border, ring), border-radius, font-family (display + body), font-size scale, spacing scale, shadow scale, transition durations.
- Configure tailwind.config.ts extending from CSS variables.
- Set up cn() utility in `src/lib/utils.ts`. Install: `npm install clsx tailwind-merge`
- Initialize shadcn: `npx shadcn init`
- Add base components: `npx shadcn add button card input label badge dialog dropdown-menu table`
- Add shadcnblocks registry to components.json:
  ```json
  "registries": {
    "@shadcnblocks": {
      "url": "https://shadcnblocks.com/r/{name}"
    }
  }
  ```
  Then install blocks via CLI: `npx shadcn add @shadcnblocks/hero-1`, `npx shadcn add @shadcnblocks/login-1`, etc. The CLI handles all dependencies automatically.
  For Pro/Premium blocks, add API key to `.env` (`SHADCNBLOCKS_API_KEY=sk_live_...`) and auth header to the registry config:
  ```json
  "headers": {
    "Authorization": "Bearer ${SHADCNBLOCKS_API_KEY}"
  }
  ```

**For Chakra UI:**
- Create theme.ts extending Chakra's default theme with custom colors, fonts, spacing, radii, shadows.
- Wrap app in ChakraProvider with custom theme.

**For MUI:**
- Create theme.ts with createTheme(). Set palette, typography (display + body), spacing, shape, shadows.
- Wrap app in ThemeProvider.

**For Mantine:**
- Create theme.ts with MantineProvider theme object.
- Set colors, primaryColor, fontFamily (display + body), spacing, radius, shadows.

**For Tailwind only:**
- Create globals.css with CSS custom properties (same token set as shadcn).
- Configure tailwind.config.ts extending from CSS variables.

**Key rule:** the token file IS the aesthetic. If you removed all components and rebuilt from scratch using only the tokens, the product should still look the same. No creative decisions in component code.

### 4. Write Design Configuration

Add the design system config to the Design System section of CLAUDE.md:

```
## Design System
**Framework:** [detected]
**Token source:** [globals.css / theme.ts / etc.]
**Component library:** [shadcn / Chakra / MUI / Mantine / none]
**Direction:** [chosen aesthetic in one sentence: "clean and authoritative — tight spacing, monospace accents, muted palette"]
**Typography:** [display font] + [body font]
**Color character:** [what the palette feels like, not just the values]
```

This is what Claude reads at session start. It prevents aesthetic drift across sessions.

---

## Review Mode

Two categories of rules: **universal** (always apply, framework-agnostic) and **framework-specific** (adapt to detected stack).

### Universal Rules (always enforced)

These are objective, measurable standards. They apply regardless of UI framework.

**Accessibility (WCAG 2.1 AA):**
1. Color contrast: 4.5:1 normal text, 3:1 large text
2. Focus states on all interactive elements
3. Alt text on all images
4. Labels on all form inputs (not just placeholder)
5. Touch targets: 44x44px minimum
6. Semantic HTML: proper heading hierarchy, landmarks, ARIA where needed
7. Keyboard navigation: all interactive elements reachable via tab

**Token compliance:**
8. No hardcoded color values (hex, rgb, hsl) in component files. All colors from design tokens (CSS variables, theme object, or equivalent).

**Interaction:**
9. All clickable elements must have `cursor-pointer` (buttons, links, cards with onClick, toggles, tabs, dropdowns). No flat cursor on interactive elements.

**Text encoding:**
10. No Unicode escape sequences for accented characters. Use UTF-8 directly: `è`, `é`, `ù`, `à`, not `\u00e8`, `\u00e9`, `\u00f9`, `\u00e0`. Applies to all text content in components, labels, placeholders, error messages.

**Responsive:**
11. Layout works at 320px minimum
12. Consistent breakpoint usage

### Framework-Specific Rules

Detect the UI framework and apply only the relevant rules.

**shadcn + Tailwind projects:**
1. Before building custom sections, check the shadcnblocks registry via CLI: `npx shadcn add @shadcnblocks/<block>`. Covers: hero, pricing, dashboard, auth, settings, features, testimonials, FAQ, footer, navbar, sidebar, stats, CTA, contact, blog, 404.
2. Use shadcn base components before custom implementations
3. No custom CSS classes — Tailwind utility classes only
4. No arbitrary Tailwind values — no `-[...]` syntax

**Chakra UI projects:**
1. Use Chakra components before custom implementations
2. All styling through Chakra's style props or theme — no inline styles
3. Responsive styles through Chakra's array/object syntax, not media queries

**MUI projects:**
1. Use MUI components before custom implementations
2. All styling through sx prop or styled() — no inline CSS
3. Theme overrides in theme.ts, not per-component

**Mantine projects:**
1. Use Mantine components before custom implementations
2. All styling through Mantine's styles API or sx — no inline CSS

**Tailwind-only projects:**
1. No custom CSS classes — Tailwind utility classes only
2. No arbitrary Tailwind values — no `-[...]` syntax

**No framework detected:**
- Only universal rules apply. No framework-specific enforcement.

### Aesthetic Consistency

Check that the chosen direction (from CLAUDE.md Design System) is being followed:

13. No font-family declarations in component files — all typography from tokens
14. No one-off shadows, border-radius, or transition values — use the token scale
15. No inline styles that override the token system
16. Components on different pages should feel like the same product — same spacing rhythm, same surface treatment, same color usage patterns

Flag violations as: "aesthetic drift — [what's happening] — should use [which token]."

### Design Quality

When reviewing component code, also check for:

- **Cohesion across pages.** Do all pages look like they belong to the same product? Same type scale, same spacing, same color usage. If page A uses generous whitespace and page B is cramped, flag it.
- **Motion and interaction.** Are transitions present where they improve UX (page transitions, loading states, hover feedback)? Are they smooth and purposeful?
- **Layout composition.** Does the layout use the design system's grid/spacing consistently?

These are advisory observations, not blocking rules. Note them as suggestions.

### Output

Read CLAUDE.md for project context (stack, design system config). Write findings to `.superskills/report.md` — **replace** the Design Findings section (each audit is a fresh snapshot). Update the status counts at the top of report.md.

| File:line | Rule | Issue | Fix |
|-----------|------|-------|-----|
| path:line | universal/N or [framework]/N or a11y or responsive or quality | description | fix |

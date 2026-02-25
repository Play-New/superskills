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

### 2. Set Up Design Tokens

Regardless of framework, establish a token system. For shadcn projects, also suggest the shadcnblocks registry for pre-built page sections.

**For shadcn + Tailwind:**
- Create globals.css with CSS custom properties in HSL. Both `:root` (light) and `.dark` (dark).
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
- Create theme.ts extending Chakra's default theme with custom colors, fonts, spacing.
- Wrap app in ChakraProvider with custom theme.

**For MUI:**
- Create theme.ts with createTheme(). Set palette, typography, spacing.
- Wrap app in ThemeProvider.

**For Mantine:**
- Create theme.ts with MantineProvider theme object.
- Set colors, primaryColor, fontFamily, spacing.

**For Tailwind only:**
- Create globals.css with CSS custom properties.
- Configure tailwind.config.ts extending from CSS variables.
- No component library enforcement.

**For any framework:** ask for brand assets — color palette (primary, secondary, accent, destructive), font family, logo path. Map to the framework's token system.

### 3. Write Design Configuration

Add the design system config to the Design System section of CLAUDE.md (this is stable project configuration, not a finding):

```
## Design System
**Framework:** [detected]
**Token source:** [globals.css / theme.ts / etc.]
**Component library:** [shadcn / Chakra / MUI / Mantine / none]
```

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

### Design Quality

When reviewing component code, also check for:

- **Intentional aesthetics.** Are components visually cohesive or piecemeal? Do typography, spacing, and color choices follow a consistent direction?
- **Motion and interaction.** Are transitions present where they improve UX (page transitions, loading states, hover feedback)? Are they smooth and purposeful?
- **Layout composition.** Does the layout use the design system's grid/spacing consistently? Are there spacing inconsistencies?

These are advisory observations, not blocking rules. Note them as suggestions.

### Output

Read CLAUDE.md for project context (stack, design system config). Write findings to `.superskills/report.md` — **replace** the Design Findings section (each audit is a fresh snapshot). Update the status counts at the top of report.md.

| File:line | Rule | Issue | Fix |
|-----------|------|-------|-----|
| path:line | universal/N or [framework]/N or a11y or responsive or quality | description | fix |

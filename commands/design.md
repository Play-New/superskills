---
description: Design system. First run sets up brand, tokens, shadcn. After that, hard rules + a11y audit.
allowed-tools: Read, Glob, Grep, Write, Edit, Bash
---

# Design

## Detect Mode

Check for globals.css with CSS custom properties OR components.json in the project. If found, run **review mode**. Otherwise, run **init mode**.

---

## Init Mode

1. **Brand assets.** Ask for: color palette (primary, secondary, accent, destructive) in HSL, font family (sans, mono), logo path.

2. **components.json** with shadcnblocks registry:
   ```json
   {
     "$schema": "https://ui.shadcn.com/schema.json",
     "style": "default",
     "rsc": true,
     "tsx": true,
     "tailwind": {
       "config": "tailwind.config.ts",
       "css": "src/app/globals.css",
       "baseColor": "neutral",
       "cssVariables": true
     },
     "aliases": {
       "components": "@/components",
       "utils": "@/lib/utils",
       "ui": "@/components/ui",
       "lib": "@/lib",
       "hooks": "@/hooks"
     },
     "registries": {
       "@shadcnblocks": {
         "url": "https://shadcnblocks.com/r"
       }
     }
   }
   ```

3. **globals.css** with CSS custom properties for all colors in HSL. Both `:root` (light) and `.dark` (dark) variants. No hardcoded hex or rgb.

4. **tailwind.config.ts** extending from CSS variables.

5. **cn() utility** in `src/lib/utils.ts`. Install: `npm install clsx tailwind-merge`

6. **Initialize shadcn:** `npx shadcn init`

7. **Base components:** `npx shadcn add button card input label badge dialog dropdown-menu table`

8. **Ask about layouts.** shadcnblocks has 1351 blocks across 90+ categories. Suggest based on project type:
   - Dashboard: `npx shadcn add @shadcnblocks/sidebar10`
   - Auth: `npx shadcn add @shadcnblocks/login1`
   - Hero: `npx shadcn add @shadcnblocks/hero125`
   - Pricing: `npx shadcn add @shadcnblocks/pricing3`

---

## Review Mode

### 5 Hard Rules

Scan all component files (`**/*.tsx`). For each rule: PASS or FAIL with file:line.

1. **shadcnblocks FIRST.** Any UI section replaceable by a shadcnblocks block? (hero, pricing, dashboard, auth, settings, features, testimonials, FAQ, footer, navbar, sidebar, stats, CTA, contact, blog, 404). 1351 blocks across 90+ categories.
2. **shadcn base SECOND.** Any custom component duplicating a shadcn component?
3. **No custom CSS classes.** Any non-Tailwind class names? No .my-card, no .custom-header.
4. **Token compliance.** Any hardcoded hex (#xxx), rgb(), hsl() values in component files? All colors from CSS variables.
5. **No arbitrary Tailwind values.** Any `-[...]` syntax? No text-[#FF5733], no p-[13px].

### Accessibility (WCAG 2.1 AA)

- Color contrast: 4.5:1 normal text, 3:1 large text
- Focus states on interactive elements
- Alt text on images
- Labels on form inputs
- Touch targets: 44x44px minimum
- Semantic HTML and ARIA where needed

### Responsive

- Mobile layout works at 320px minimum
- Consistent breakpoint usage (sm/md/lg/xl)

### Output

Append to CLAUDE.md Design Findings:

| File:line | Rule | Issue | Fix |
|-----------|------|-------|-----|
| path:line | Rule N or a11y or responsive | description | fix, include shadcn/shadcnblocks command if applicable |

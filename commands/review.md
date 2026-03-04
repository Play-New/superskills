---
description: Full quality audit. Tests, security, strategy alignment, design consistency, performance. All at once.
allowed-tools: Read, Glob, Grep, Write, Edit, Bash
---

# Review

Full audit of the project. Run tests first (broken code makes other audits unreliable), then the remaining four domains. If agent teams are available, run the remaining four in parallel. Otherwise, run everything sequentially.

## Prerequisites

Read CLAUDE.md. If no EIID mapping exists, stop and suggest `/super:strategy` first.

---

## 1. Tests

### Detect Mode

Check for vitest.config.ts OR a `test` script in package.json. If found, run **verify**. Otherwise, run **init**.

### Init

1. **Install dependencies:**
   ```bash
   npm install -D vitest @playwright/test
   npx playwright install
   ```

2. **Configure vitest.** Create or update vitest.config.ts. Set up test directory structure: `tests/unit/`, `tests/e2e/`.

3. **Add scripts to package.json:**
   ```json
   {
     "test": "vitest run",
     "test:watch": "vitest",
     "test:e2e": "npx playwright test"
   }
   ```

4. **Configure Playwright.** Create playwright.config.ts with local dev server and base URL.

5. **Stack-adaptive setup:**
   - **Supabase:** mock client for unit tests, test RLS with different roles
   - **Inngest:** use `inngest/test` for step function testing
   - **Next.js:** mock `NextRequest` for API route tests

6. **Write smoke tests:** unit smoke test (core utilities work), E2E smoke test (home page loads and renders).

7. **Run tests to verify:** `npm test`

### Verify

Run in sequence:

1. `npm test -- --run`
2. If Playwright configured: `npx playwright test`
3. `npx tsc --noEmit`

### Gate

**Block while tests fail.** Do not proceed to remaining audits automatically. Ask whether to continue with the remaining audits or stop to fix failures first.

---

## 2. Security

Severity: blocking on critical findings, HIGH/MEDIUM/LOW otherwise.

Read `reference/review-security-guide.md` for the full security audit checklist. Apply all checks relevant to the detected stack. Block on: credentials in code, SQL/NoSQL injection, XSS, authentication bypass, PII exposure without consent.

---

## 3. Strategy

Read CLAUDE.md EIID mapping. For each source file:
1. Which EIID layer does it support? (enrichment / inference / interpretation / delivery / none)
2. If "none": is it supporting infrastructure (tests, types, config, build, utilities used by EIID layers)? Expected, not scope creep. If it's application code that doesn't serve any EIID layer, flag as potential scope creep.
3. Any dependency not traceable to the EIID mapping? Flag it.

Run the opportunity scan:

**Enrichment:**
1. Any data source connected but not used for inference?
2. Any two data sources that could be cross-referenced but are not?
3. Any public API or external data that would fill a gap in the current schema?

**Inference:**
4. Any collected data with patterns not being analyzed?
5. Any prediction the existing data supports but nobody built?
6. Any anomaly detection that would catch problems before users notice?

**Interpretation:**
7. Any analysis result generated but not surfaced to users?
8. Any insight that lacks context (trend, comparison, explanation) to be actionable?

**Delivery:**
9. Any channel the users frequent that the system does not reach?
10. Any timing improvement (deliver sooner, deliver at the right moment)?
11. Any trigger condition that would catch events currently missed?

---

## 4. Design

Read CLAUDE.md and `.superskills/design-system.md` for context. Read `reference/design-critique.md` for the full critique framework. Work through all six layers (0 through 5).

### EIID Modality Alignment

Severity: blocking if widespread, warning if isolated.

Read `.superskills/design-system.md` for the EIID Interface Map. If no EIID Interface Map section exists, skip this subsection.

1. **Visual scope match:** Are there screens or pages for EIID layers mapped to non-visual modalities? Each is a candidate for removal or conversion to a conversational pattern.
2. **Missing visual surfaces:** Are visual-mapped layers missing screens or components? Each gap means the product can't deliver on that layer's promise.
3. **Channel coverage:** Are conversational or notification layers implemented? Missing channels mean delivery failures.
4. **Cross-channel consistency:** Do the same terms, metrics, and framing appear in both visual and conversational surfaces? Inconsistency confuses users who use multiple channels.

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

### Design System Compliance (blocking if widespread)

If `.superskills/design-system.md` has Layout, Typography Scale, and Composition sections, check compliance:
1. **Layout:** components use documented grid, breakpoints, container strategy, and page patterns. Flag undocumented breakpoints or layouts that bypass the grid.
2. **Typography:** text elements use levels from the documented scale. Flag sizes or weights not in the scale.
3. **Composition:** density zones match the documented density map. Section spacing matches the documented rhythm. Flag uniform spacing where the map says it should vary.

If no design system with these sections exists, skip.

### Cross-File Consistency (blocking if widespread)

Compare across all component files:
1. **Type scale:** same heading sizes, body text sizes, and weights everywhere
2. **Spacing rhythm:** consistent spacing multiples across pages
3. **Surface treatment:** same card styles, same border usage, same shadow levels
4. **Color usage:** same semantic colors for same purposes

### Framework Rules

- **shadcn + Tailwind:** utility classes only, `gap-*` for containers, semantic tokens only, `data-slot` attributes, CVA for variants, spacing matches style preset
- **Chakra/MUI/Mantine:** all styling through framework APIs, theme overrides in theme file
- **Tailwind only:** utility classes only, no arbitrary values

### Craft (advisory)

Read `reference/design-critique.md` for the full critique framework. Read `reference/design-craft.md` for execution guidance. Apply all six critique layers, evaluating five craft dimensions:

1. **Spatial composition:** density variation between zones, intentional asymmetry, grid-breaking at focal points, negative space as grouping. Uniform spacing everywhere signals undesigned.
2. **Typography:** hierarchy works without color (size + weight alone produce three tiers), fonts chosen intentionally (not framework defaults), monospace for aligned data.
3. **Surfaces and depth:** subtle lightness shifts between levels (not color jumps), consistent depth strategy (not mixed), opacity-based borders, complete interactive states.
4. **Identity:** signature element visible, committed direction (not half-measures), anti-convergence test, atmosphere matches direction (or deliberate absence).
5. **Conversational and notification craft:** Do messages lead with insight? Is information density appropriate for the channel? Are interaction patterns native to the platform? Is delivery timing designed? Read `reference/design-craft.md` Conversational and Notification Craft section.

Flag craft issues as suggestions, not violations.

---

## 5. Performance

Severity: blocking on regressions beyond budget, advisory otherwise.

Read `reference/review-performance-guide.md` for the full performance audit checklist. Init mode establishes baseline targets and tooling. Review mode measures against those targets. Block on: bundle size regression >20%, missing lazy loading for routes, unindexed queries on user-facing paths.

---

## Output

Write all findings to `.superskills/`:

- Test results → **replace** Test Report section in `.superskills/report.md` (keep last 3 runs)
- Security findings → **replace** Security Findings section in `.superskills/report.md`
- Strategy findings → **append** to `.superskills/decisions.md`
- Design findings → **replace** Design Findings section in `.superskills/report.md`
- Performance findings → **replace** Performance Budget section in `.superskills/report.md`

**Update status counts** at the top of `.superskills/report.md`.

**Update Project Profile** in `.superskills/report.md`: note which EIID layers have the most code, which issues recur, and any patterns learned.

Print a summary at the end:

```
## Review Summary — [date]

Tests:        [passed/failed/skipped]
Security:     [blocking count] blocking, [high count] high
Strategy:     [scope-creep count] scope creep, [opportunity count] opportunities
Design:       [violation count] violations
Performance:  [issue count] issues

Blocking:     [yes/no]
```

Security output table format:

| Severity | File:line | Issue | Fix |
|----------|-----------|-------|-----|
| BLOCK/HIGH/MEDIUM/LOW | path:line | description | what to do |

Performance output table format:

| Category | Issue | Impact | Fix | Estimated Savings |
|----------|-------|--------|-----|-------------------|
| bundle/cwv/database/api-costs/caching | description | quantified | what to change | time or money saved |

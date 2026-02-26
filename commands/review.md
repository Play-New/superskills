---
description: Full project review. Security, design, strategy, performance, tests — all at once.
allowed-tools: Read, Glob, Grep, Write, Edit, Bash
---

# Review

Full audit of the project. If agent teams are available, run tests first then the other four audits in parallel. Otherwise, run everything sequentially.

## Prerequisites

Read CLAUDE.md. If no EIID mapping exists, stop and suggest `/super:start` first.

## 1. Tests

Run `npm test -- --run`. If Playwright configured, run `npx playwright test`. Run `npx tsc --noEmit`.

If tests fail, report failures and ask whether to continue with the remaining audits or stop.

## 2. Trust

Scan all source files for:
1. Hardcoded API keys, passwords, tokens, secrets
2. SQL injection (unsanitized user input in queries)
3. XSS (unescaped user content rendered as HTML)
4. Auth bypass (missing auth checks on protected routes)
5. PII exposure (personal data in logs, errors, URLs)
6. Missing RLS policies on Supabase tables
7. Secrets in environment config committed to git
8. OWASP Top 10 full scan
9. GDPR compliance (consent, export, deletion, minimization, retention)
10. Stack-adaptive checks (Supabase, Vercel, Inngest, Next.js)

## 3. Strategy

Read CLAUDE.md EIID mapping. For each source file:
1. Which EIID layer does it support? (enrichment / inference / interpretation / delivery / none)
2. If "none", flag as scope creep.
3. Any dependency not traceable to the EIID mapping? Flag it.

Run the opportunity scan:
- Enrichment: unused data sources? Cross-references possible? External APIs to fill gaps?
- Inference: unanalyzed patterns? Predictions not built? Missing anomaly detection?
- Interpretation: results not surfaced? Insights lacking context?
- Delivery: unreached channels? Timing improvements? Missed triggers?

## 4. Design

Token compliance and component discovery are handled continuously by the hook and skill. Review catches what automation misses.

Accessibility (blocking):
1. Color contrast 4.5:1 normal, 3:1 large
2. Focus states on interactive elements
3. Alt text on images
4. Labels on form inputs
5. Touch targets 44x44px
6. Semantic HTML: heading hierarchy, landmarks, ARIA
7. Keyboard navigation: all interactive elements reachable via tab
8. `cursor-pointer` on all clickable elements

Cross-file consistency (blocking if widespread):
1. Type scale: same sizes and weights everywhere
2. Spacing rhythm: consistent multiples across pages
3. Surface treatment: same card/border/shadow patterns
4. Color usage: same semantic colors for same purposes

Craft (advisory):
1. Name test: describe the UI to someone who cannot see it. Generic description = generic design.
2. Swap test: swap typeface or layout for a default. Where it wouldn't matter = where you defaulted.
3. Spatial composition: intentional asymmetry, overlap, density variation?
4. Atmosphere: backgrounds and surfaces create a sense of place?
5. Motion: one orchestrated page load beats scattered micro-interactions.

## 5. Performance

Check:
- Bundle sizes, largest dependencies, missing dynamic imports, tree-shaking
- Core Web Vitals: LCP sources, INP blocking, CLS shifts
- Database: N+1 patterns, missing indexes, over-fetching, under-batching
- API costs: LLM call sites, token estimates, $/month projection, caching opportunities
- Stack-adaptive: Supabase queries, Vercel ISR/edge, Inngest batching, Next.js Server Components

## Output

Write all findings to `.superskills/`:

- Test results → **replace** Test Report in `.superskills/report.md` (keep last 3 runs)
- Trust findings → **replace** Security Findings in `.superskills/report.md`
- Strategy findings → **append** to `.superskills/decisions.md`
- Design findings → **replace** Design Findings in `.superskills/report.md`
- Performance findings → **replace** Performance Budget in `.superskills/report.md`

**Update status counts** at the top of `.superskills/report.md`.

**Update Project Profile** in `.superskills/report.md`: note which EIID layers have the most code, which issues recur, and any patterns learned.

Print a summary at the end:

```
## Review Summary — [date]

Tests:        [passed/failed/skipped]
Trust:        [blocking count] blocking, [high count] high
Strategy:     [scope-creep count] scope creep, [opportunity count] opportunities
Design:       [violation count] violations
Performance:  [issue count] issues

Blocking:     [yes/no]
```

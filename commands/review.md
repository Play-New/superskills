---
description: Full project review. Uses agent teams for parallel audit when available, sequential fallback otherwise.
allowed-tools: Read, Glob, Grep, Write, Edit, Bash
---

# Review

Comprehensive project audit. Runs each skill's review mode and synthesizes findings.

## Prerequisites

Read CLAUDE.md. If no EIID mapping exists, stop and suggest `/super:start` first.

## Agent Team Mode

If agent teams are enabled (`CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS`), create a team of teammates. Each gets its own context and works in parallel. They can share findings with each other.

### Team Structure

**testing-reviewer** (runs first, others wait for it)
Run `npm test -- --run`. If Playwright tests exist in tests/e2e/ or e2e/, run `npx playwright test`. Run `npx tsc --noEmit`. Report pass/fail counts with failure details. If tests fail, flag as blocking.

**trust-reviewer** (after tests pass)
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

Flag BLOCK items: hardcoded credentials, SQL injection, XSS, auth bypass, PII exposure.
Share BLOCK findings with all teammates so they can account for them.

**strategy-reviewer** (after tests pass)
Read CLAUDE.md EIID mapping. For each source file:
1. Which EIID layer does it support? (enrichment / inference / interpretation / delivery / none)
2. If "none", flag as scope creep.
3. Any dependency not traceable to the EIID mapping? Flag it.

Run the 11-question opportunity scan:
- Enrichment: unused data sources? Cross-references possible? External APIs to fill gaps?
- Inference: unanalyzed patterns? Predictions not built? Missing anomaly detection?
- Interpretation: results not surfaced? Insights lacking context?
- Delivery: unreached channels? Timing improvements? Missed triggers?

Share scope creep findings with trust-reviewer (scope creep often introduces unreviewed attack surface).

**design-reviewer** (after tests pass)
Detect UI framework from package.json, then scan all component files.

Universal rules (always):
1. Color contrast 4.5:1 normal, 3:1 large
2. Focus states on interactive elements
3. Alt text on images
4. Labels on form inputs
5. Touch targets 44x44px
6. No hardcoded color values in components — use design tokens
7. `cursor-pointer` on all clickable elements
8. No Unicode escape sequences for accents — use UTF-8 directly
9. Responsive: works at 320px, consistent breakpoints

Framework-specific rules (adapt to detected stack):
- shadcn + Tailwind: check shadcnblocks registry via CLI before custom sections, shadcn base second, no custom CSS, no arbitrary Tailwind
- Chakra/MUI/Mantine: use framework components before custom, style through framework APIs
- Tailwind only: no custom CSS classes, no arbitrary values

Share component-level findings with performance-reviewer (design violations often correlate with bundle issues).

**performance-reviewer** (after tests pass)
Check:
- Bundle sizes, largest dependencies, missing dynamic imports, tree-shaking
- Core Web Vitals: LCP sources, INP blocking, CLS shifts
- Database: N+1 patterns, missing indexes, over-fetching, under-batching
- API costs: LLM call sites, token estimates, $/month projection, caching opportunities
- Stack-adaptive: Supabase queries, Vercel ISR/edge, Inngest batching, Next.js Server Components

Cross-reference with design-reviewer findings on heavy custom components.

### Synthesis

After all teammates finish, the lead reads all findings and writes a consolidated report.

## Sequential Fallback

If agent teams are not enabled, run each review sequentially in this order:

1. **Tests.** Run `npm test -- --run`. If Playwright configured, run `npx playwright test`. Run `npx tsc --noEmit`. If tests fail, report failures and ask whether to continue with the remaining audits or stop.

2. **Trust.** OWASP Top 10 scan, secrets scan, GDPR compliance, stack-adaptive checks. Flag BLOCK items.

3. **Strategy.** EIID alignment check on all source files. 11-question opportunity scan.

4. **Design.** Detect UI framework, apply universal rules (accessibility, tokens, responsive) + framework-specific rules.

5. **Performance.** Bundle analysis, Core Web Vitals, database patterns, API costs, stack-adaptive checks.

## Output

Read CLAUDE.md for project context (stack, EIID mapping, constraints). Write all findings to `.superskills/`:

- Test results → **replace** Test Report in `.superskills/report.md` (keep last 3 runs)
- Trust findings → **replace** Security Findings in `.superskills/report.md`
- Strategy findings → **append** to `.superskills/decisions.md`
- Design findings → **replace** Design Findings in `.superskills/report.md`
- Performance findings → **replace** Performance Budget in `.superskills/report.md`

**Update status counts** at the top of `.superskills/report.md`.

**Update Project Profile** in `.superskills/report.md`: note which EIID layers have the most code coverage, which issue types recur, and any project-specific patterns learned.

At the end, print a summary:

```
## Review Summary — [date]

Tests:        [passed/failed/skipped]
Trust:        [BLOCK count] blocking, [HIGH count] high, [MEDIUM count] medium
Strategy:     [scope-creep count] scope creep, [opportunity count] opportunities
Design:       [violation count] violations, [a11y count] accessibility issues
Performance:  [issue count] issues, [estimated savings]

Blocking:     [yes/no — yes if any test failure, trust BLOCK, or test BLOCK]
```

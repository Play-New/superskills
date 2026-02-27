---
description: Full quality audit. Tests, security, strategy alignment, design consistency, performance — all at once.
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

### OWASP Top 10

Scan all source files. For each item: PASS or FAIL with file:line.

1. **Broken access control.** API routes without auth checks? Missing RLS?
2. **Injection.** Unsanitized user input in queries, commands, templates?
3. **XSS.** Unescaped user content rendered as HTML?
4. **SSRF.** User-provided URLs fetched server-side without validation?
5. **Security misconfiguration.** Debug mode on? CORS too permissive? Missing headers?
6. **Vulnerable components.** Run `npm audit`. Flag critical/high.
7. **Auth failures.** Weak session handling? Missing rate limiting?
8. **Data integrity.** Missing input validation on Server Actions or API routes?
9. **Logging failures.** Security events not logged? PII in logs?
10. **CSRF.** State-changing actions without CSRF tokens? Missing SameSite cookies?

### Secrets Scan

- Hardcoded API keys, passwords, tokens in source files
- `.env` or `.env.local` in .gitignore?
- Secrets in git history (search for common patterns)

### GDPR Compliance

Six checks:
1. Consent mechanism present?
2. Data export capability?
3. Data deletion capability?
4. Data minimization practiced?
5. Retention policy defined?
6. Breach notification process documented?

### Stack-Adaptive Checks

Detect from package.json. Apply only relevant sections.

**Supabase:**
- RLS enabled on all tables?
- service_role key server-side only?
- Auth middleware on protected routes?
- Storage policies configured?

**Vercel:**
- Env vars split between Preview and Production?
- Edge middleware for auth?
- Security headers configured?
- No secrets in vercel.json?

**Inngest:**
- INNGEST_SIGNING_KEY validated?
- No PII in event payloads?
- Steps idempotent?

**Next.js:**
- Server Actions validate input?
- API routes check auth?
- Dynamic route params sanitized?
- No path traversal in file operations?

### Blocking Rules

**BLOCK** (must fix before proceeding):
- Credentials hardcoded in source
- SQL injection
- XSS
- Auth bypass on protected routes
- PII exposure in logs, errors, or URLs

Everything else: severity rated HIGH / MEDIUM / LOW.

---

## 3. Strategy

Read CLAUDE.md EIID mapping. For each source file:
1. Which EIID layer does it support? (enrichment / inference / interpretation / delivery / none)
2. If "none", flag as scope creep.
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

## 4. Design (abbreviated)

Full design audit is `/super:design`. Review catches the blocking issues.

### Accessibility (blocking)

WCAG 2.1 AA — 8 checks:
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
2. **Spacing rhythm:** consistent spacing multiples across pages
3. **Surface treatment:** same card styles, same border usage, same shadow levels
4. **Color usage:** same semantic colors for same purposes

### Craft (advisory)

1. **Name test:** describe the UI to someone who cannot see it. Generic description = generic design.
2. **Swap test:** swap typeface or layout for a default. Where it wouldn't matter = where you defaulted.
3. **Composition:** intentional asymmetry, overlap, density variation?
4. **Atmosphere:** backgrounds and surfaces create a sense of place?
5. **Motion:** one orchestrated page load beats scattered micro-interactions.

---

## 5. Performance

### Detect Mode

Check `.superskills/report.md` Performance Budget section. If it has content, run **review**. Otherwise, run **init** first.

### Init

1. Set targets:
   - LCP < 2.5s
   - Bundle < 200KB gzipped
   - API response < 500ms
   - CLS < 0.1
   - INP < 200ms

2. Configure bundle analysis: add `@next/bundle-analyzer` to devDependencies, add `analyze` script.

3. Set up Web Vitals reporting in layout.tsx.

4. Write budget to `.superskills/report.md` Performance Budget section.

### Review

**Bundle analysis:**
- Run `npx next build` and check output sizes
- Identify largest dependencies
- Missing dynamic imports for heavy libraries?
- Tree-shaking working? (check for barrel file imports)

**Core Web Vitals:**
- **LCP:** largest content paint sources. Images without next/image? Fonts without next/font?
- **INP:** main thread blocking. Heavy synchronous operations?
- **CLS:** layout shift sources. Images without dimensions? Dynamic content above the fold?

**Database:**
- N+1 query patterns (queries in loops)
- Missing indexes
- Over-fetching (SELECT * instead of specific columns)
- Under-batching (multiple single inserts instead of batch)

**API costs:**
- Count LLM call sites
- Estimate tokens per call
- Estimate $/month at projected volume
- Caching opportunities for repeated calls

**Stack-adaptive checks:**

Detect from package.json. Apply only relevant sections.

**Supabase:**
- `.select('*')` usage? Use specific columns.
- Batch upsert instead of single inserts?
- Indexes on filtered/sorted columns?
- Edge Functions for compute-heavy operations?

**Vercel:**
- ISR configured for semi-static pages?
- Edge Runtime for API routes that need low latency?
- next/image for all images?
- next/font for all fonts?

**Inngest:**
- step.sleep() instead of polling loops?
- Batching for high-frequency events?
- Concurrency limits set?

**Next.js:**
- Dynamic imports for heavy client components?
- Server Components by default, Client Components only when needed?
- Streaming for slow data sources?
- Route segment config for static/dynamic?

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

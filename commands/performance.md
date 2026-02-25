---
description: Performance. First run sets budgets. After that, bundle, CWV, N+1, API cost audit.
allowed-tools: Read, Glob, Grep, Write, Edit, Bash
---

# Performance

## Detect Mode

Check `.superskills/report.md`. If Performance Budget section has content, run **review mode**. Otherwise, run **init mode**.

---

## Init Mode

1. Set targets:
   - LCP < 2.5s
   - Bundle < 200KB gzipped
   - API response < 500ms
   - CLS < 0.1
   - INP < 200ms

2. Configure bundle analysis: add `@next/bundle-analyzer` to devDependencies, add `analyze` script.

3. Set up Web Vitals reporting in layout.tsx.

4. Write budget to `.superskills/report.md` Performance Budget section.

---

## Review Mode

### Bundle Analysis

- Run `npx next build` and check output sizes
- Identify largest dependencies
- Missing dynamic imports for heavy libraries?
- Tree-shaking working? (check for barrel file imports)

### Core Web Vitals

- **LCP:** largest content paint sources. Images without next/image? Fonts without next/font?
- **INP:** main thread blocking. Heavy synchronous operations?
- **CLS:** layout shift sources. Images without dimensions? Dynamic content above the fold?

### Database

- N+1 query patterns (queries in loops)
- Missing indexes
- Over-fetching (SELECT * instead of specific columns)
- Under-batching (multiple single inserts instead of batch)

### API Costs

- Count LLM call sites
- Estimate tokens per call
- Estimate $/month at projected volume
- Caching opportunities for repeated calls

### Stack-Adaptive Checks

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

### Output

Read CLAUDE.md for project context (stack, EIID mapping). Write findings to `.superskills/report.md` — **replace** the Performance Budget section (each audit is a fresh snapshot). Update the status counts at the top of report.md.

| Category | Issue | Impact | Fix | Estimated Savings |
|----------|-------|--------|-----|-------------------|
| bundle/cwv/database/api-costs/caching | description | quantified | what to change | time or money saved |

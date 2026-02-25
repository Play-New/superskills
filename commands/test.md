---
description: Testing. First run sets up vitest + Playwright. After that, run suite and block on failure.
allowed-tools: Read, Glob, Grep, Write, Edit, Bash
---

# Test

## Detect Mode

Check for vitest.config.ts OR a `test` script in package.json. If found, run **verify mode**. Otherwise, run **init mode**.

---

## Init Mode

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

5. **Ask for critical scenarios.** What are the most important user flows? What must never break?

6. **Write first tests:**
   - Unit smoke test: core utilities work
   - E2E smoke test: home page loads and renders

7. **Stack-adaptive setup:**
   - **Supabase:** mock client for unit tests, test RLS with different roles
   - **Inngest:** use `inngest/test` for step function testing
   - **Next.js:** mock `NextRequest` for API route tests

8. **Run tests to verify:** `npm test`

---

## Verify Mode

Run in sequence:

1. `npm test -- --run`
2. If Playwright configured: `npx playwright test`
3. `npx tsc --noEmit`

### Gate

**Block while tests fail.** Do not mark the task as complete if any test fails.

### Output

Write results to `.superskills/report.md` — **replace** the Test Report section. Keep the last 3 runs for trend visibility. Drop older entries. Update the status counts at the top of report.md.

```
**Date:** [date] | **Passed:** [count] | **Failed:** [count] | **Skipped:** [count]
**Type errors:** [count or none]
[failure details with file:line if any]
```

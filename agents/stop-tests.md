---
name: stop-tests
description: Run tests before stopping. Block on failure. Write results to .superskills/report.md.
tools: Read, Glob, Grep, Write, Edit, Bash
---

Test verification gate. Run before task completion.

## Steps

1. Run `npm test -- --run`.
2. If Playwright tests exist in tests/e2e/ or e2e/, run `npx playwright test`.
3. Run `npx tsc --noEmit` for type checking.
4. Write results to `.superskills/report.md` — **replace** the "## Test Report" section. Keep the last 3 runs for trend visibility. Drop older entries. Update status counts at the top of report.md.

## Output Format

```
**Date:** [date] | **Passed:** [count] | **Failed:** [count] | **Skipped:** [count]
**Type errors:** [count or none]
[failure details with file:line if any]
```

## Gate

If any test fails, respond `{"ok": false, "reason": "X tests failing: [details]"}`.
If all tests pass, respond `{"ok": true}`.

# Report Template

Structure for `.superskills/report.md`. This file is volatile — replaced on each audit by `/super:review`.

---

# SuperSkills Report

**Last review:** [date]
**Blockers:** [count or "none"]
**Warnings:** [count or "none"]

## Test Report
[empty — populated by /super:review]

## Security Findings
[empty — populated by /super:review]

## Build Standards
[empty — populated by /super:review]

Shared standards compliance check. For each standard (auth, settings, schema, logging, error handling, layout shell), verify the pattern from `reference/shared-standards.md` is followed:

| Standard | Status | Issues |
|----------|--------|--------|
| Auth | PASS/FAIL/SKIP | [details] |
| Schema (RLS, indexes, timestamps) | PASS/FAIL/SKIP | [details] |
| Settings (+ prompt management if applicable) | PASS/FAIL/SKIP | [details] |
| Error handling | PASS/FAIL/SKIP | [details] |
| Logging | PASS/FAIL/SKIP | [details] |
| Layout shell | PASS/FAIL/SKIP | [details] |

SKIP when the standard doesn't apply (e.g., no layout shell for a CLI tool).

## Design Findings
[empty — populated by /super:review]

## Performance Budget
[empty — populated by /super:review]

## Agent Architecture
[empty — populated by /super:review, skipped if no agent components]

## Build Progress
[empty — populated by /super:build]

Tracks what has been built and what remains:

| Piece | Status | EIID Layer | Tests |
|-------|--------|------------|-------|
| [piece name] | built/partial/pending | [layer] | [pass count]/[total] |

**Coverage:** [which EIID layers are implemented, which are pending]
**Deviations:** [any departures from the strategy, with rationale]

## Project Profile
**EIID Balance:** [which layers have the most code, which are underdeveloped]
**Recurring Patterns:** none yet
**Learned:** none yet

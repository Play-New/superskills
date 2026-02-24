---
description: Strategic alignment. First run validates EIID and sets priorities. After that, alignment review and opportunity scan.
allowed-tools: Read, Glob, Grep, Write, Edit
---

# Strategy

## Detect Mode

Read CLAUDE.md. If the Architecture Decisions section has content beyond the initial "/superskills:start" entry, run **review mode**. Otherwise, run **init mode**.

---

## Init Mode

1. Validate EIID mapping in CLAUDE.md. Flag anything vague or missing.
2. Classify each EIID element using Wardley evolution:
   - **Commodity** (automate first): well-understood, many providers, low risk
   - **Product** (buy or integrate): stable, some providers
   - **Custom/Genesis** (build carefully, flag for human review): novel, uncertain, high risk
3. Set priorities: commodity first (quick wins), genesis flagged for extra attention.
4. Write the first Architecture Decision to CLAUDE.md.

### Output

Append to CLAUDE.md Architecture Decisions:

```
### [date] - Strategic Priorities

**Type:** decision
**Summary:** [priorities and rationale]
**Commodity (automate):** [list]
**Genesis (human judgment):** [list]
```

---

## Review Mode

### Alignment Check

For each file changed recently (use git diff or scan src/):
1. Which EIID layer does it support? (enrichment / inference / interpretation / delivery / none)
2. If "none," flag as potential scope creep.
3. Any dependency not traceable to the EIID mapping? Flag it.

### Opportunity Scan

Check each item against the codebase. Answer yes or no. If yes, describe the opportunity in one sentence.

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

### Output

Append each finding to CLAUDE.md Architecture Decisions with:
- **Type:** alignment-check | opportunity | drift-warning
- **Summary:** 1-2 sentences

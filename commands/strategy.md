---
description: Strategic alignment. First run validates EIID and sets priorities. After that, alignment review and opportunity scan.
allowed-tools: Read, Glob, Grep, Write, Edit
---

# Strategy

## Detect Mode

Check `.superskills/decisions.md`. If it has content beyond the initial `/super:start` entry, run **review mode**. Otherwise, run **init mode**.

---

## Init Mode

1. Validate EIID mapping in CLAUDE.md. Flag anything vague or missing.
2. Classify each EIID element by constraint type:
   - **Automate** (scarcity-based value collapsing): well-understood, many providers, AI eroding the advantage
   - **Differentiate** (risk-based value increasing): requires human judgment, benefits from better information
   - **Innovate** (coordination-based value emerging): newly possible connections between systems, teams, data
3. Set priorities: automate first (quick wins), innovate flagged for careful design.
4. Write the first Architecture Decision to `.superskills/decisions.md`.

### Output

Read CLAUDE.md for project context (EIID mapping, strategic classification). **Append** to `.superskills/decisions.md`:

```
### [date] - Strategic Priorities

**Type:** decision
**Summary:** [priorities and rationale]
**Automate:** [list]
**Differentiate:** [list]
**Innovate:** [list]
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

Read CLAUDE.md for project context. **Append** each finding to `.superskills/decisions.md` (append-only — it is a decision log, history matters):
- **Type:** alignment-check | opportunity | drift-warning
- **Summary:** 1-2 sentences

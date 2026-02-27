# CLAUDE.md Template

The plugin generates this file via `/super:strategy`. It contains stable project instructions that Claude reads at session start. Target: under 100 lines. Findings go to `.superskills/`, not here.

---

```markdown
# [Project Name]

## Business
**Client:** [who is paying]
**Industry:** [sector, size, geography]

## User
**End user:** [role, daily context]
**Need:** [outcome, not feature]

## Stack
[detected or recommended, with rationale]

## EIID

### Enrichment (data)
**Have:** [sources already connected]
**Missing:** [gaps to fill]
**Connect:** [systems to integrate]

### Inference (patterns)
**Detect:** [what to spot]
**Predict:** [what to forecast]
**Flag:** [anomalies to catch]

### Interpretation (insights)
**Surface:** [what to tell the user]
**Frame as:** [comparison, trend, explanation, recommendation]

### Delivery (reach)
**Channels:** [where the user already is]
**Triggers:** [when to send]
**Timing:** [optimal moment]

## Build or Buy
**Buy:** [commodity — don't build what you can buy]
**Enhance:** [needs human judgment — give them better information]
**Build:** [new connections — this is where value is created]

## Technology Constraints
[detected constraints, one per line: "Use X, NOT Y, Z."]

## Code Architecture
- No source file over 200 lines. Split by responsibility.
- One component per file. One utility per file.
- Colocation: tests next to source, types next to usage.
- Prefer composition over inheritance.
- If a module has two distinct modes, split into separate files.

## Design System
**Framework:** [detected or recommended]
**Token source:** [globals.css / theme.ts / etc.]
```

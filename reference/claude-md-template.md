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

### Enrichment (collect)
**Have:** [sources already connected]
**Human input:** [how people naturally feed data — photos, voice, chat, email, manual entry]
**Missing:** [gaps to fill]
**Connect:** [systems to integrate]
**Approach:** [automate / differentiate / innovate — per component]

### Inference (patterns)
**Detect:** [what to spot]
**Predict:** [what to forecast]
**Flag:** [anomalies to catch]
**Approach:** [automate / differentiate / innovate — per component]

### Interpretation (insights)
**Surface:** [what to tell the user]
**Frame as:** [comparison, trend, explanation, recommendation]
**Approach:** [automate / differentiate / innovate — per component]

### Delivery (reach)
**Channels:** [same channels used for input — the system is invisible, not a destination]
**Triggers:** [when to send]
**Timing:** [optimal moment]
**Approach:** [automate / differentiate / innovate — per component]

## Technology Constraints
[detected constraints, one per line: "Use X, NOT Y, Z."]

## Code Architecture
- Split files by responsibility, not by line count. If a file does two things, split it. If it does one thing in 300 lines, leave it.
- One component per file. One utility per file.
- Colocation: tests next to source, types next to usage.
- Prefer composition over inheritance.
- If a module has two distinct modes, split into separate files.

## Design System
**Framework:** [detected or recommended]
**Token source:** [globals.css / theme.ts / etc.]
```

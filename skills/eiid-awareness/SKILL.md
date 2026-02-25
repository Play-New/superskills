---
name: eiid-awareness
description: EIID strategic awareness during planning. Fires when Claude enters plan mode or proposes architecture changes. Checks alignment with EIID mapping, flags scope creep, surfaces missed opportunities.
user-invocable: false
---

When planning changes to the codebase:

1. Read CLAUDE.md for the EIID mapping and user need. Read `.superskills/report.md` for the Project Profile (if it exists).
2. **User need check:** does this change trace back to the user need defined in CLAUDE.md? If not, why are we building it?
3. **EIID layer:** which layer does it support? (enrichment / inference / interpretation / delivery / none). No layer? Potential scope creep. Flag it.
4. **Evolution check:** is this component commodity (automate — don't build what you can buy), judgment-dependent (differentiate — enhance with better information), or new coordination (innovate — build it)?
5. **Delivery check:** does this reach the user where they already are, or does it require context-switching?
6. **Profile check:** does the Project Profile flag any recurring patterns relevant to this change? (e.g., "new API routes tend to lack auth checks" — remind about auth if adding a route.)
7. Surface missed opportunities from the 11-question scan only when clearly relevant to the current change.

One line per observation. Only when relevant. Not blocking. Brief.

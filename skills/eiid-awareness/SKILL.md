---
name: eiid-awareness
description: EIID strategic awareness during planning. Fires when Claude enters plan mode or proposes architecture changes. Checks alignment with EIID mapping, flags scope creep, surfaces missed opportunities.
user-invocable: false
---

When planning changes to the codebase:

1. Read CLAUDE.md for the EIID mapping.
2. For each proposed change: which EIID layer does it support? (enrichment / inference / interpretation / delivery / none)
3. No EIID layer? Potential scope creep. Flag it.
4. Choudary check: is this improving delivery (valuable) or duplicating commodity processing (wasteful)?
5. Wardley check: is this genesis (needs careful human judgment, flag risk) or commodity (automate confidently)?
6. Steinberger check: does this reach the user where they already are, or does it require context-switching?
7. Surface missed opportunities from the 11-question scan only when clearly relevant to the current change.

One line per observation. Only when relevant. Not blocking. Brief.

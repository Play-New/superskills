---
name: design-awareness
description: Design system awareness during planning. Fires when Claude enters plan mode or proposes UI changes. Checks token compliance, component reuse, and aesthetic consistency.
user-invocable: false
---

When planning changes to UI code:

1. If `.superskills/design-system.md` does not exist, stop. No design system configured.
2. Read `.superskills/design-system.md` for direction, tokens, and component patterns. Read CLAUDE.md for the Design System section.
3. **Component discovery:** before building any custom component, search for existing implementations. Run `npx shadcn@latest add` to explore available registries and components. Check the shadcn ecosystem: base registry, community registries (@reui, @animate-ui, @diceui, and others). If a component exists, install it instead of building custom.
4. **Direction check:** does this change match the established aesthetic direction? Flag drift from the documented feel, color world, or signature element.
5. **Pattern reuse:** if `.superskills/design-system.md` has documented measurements (heights, padding, radius), use them. Do not create new variants when an existing pattern fits.
6. **Hierarchy check:** if `.superskills/design-system.md` has an Information Architecture section, check three things. Navigation budget: if the change adds a nav item, count existing items against the budget; exceeding means something moves down or the new item nests inside an existing screen. Focal point: if the change adds content to a screen's primary zone, check if it competes with the documented focal point. Content depth: if the change surfaces something that belongs at one-click or deep tier based on documented rules, flag it.
7. **Staleness nudge:** if `.superskills/design-system.md` has a Component Patterns table, count how many UI components exist in the codebase vs. how many are documented in the table. If the gap is 5+ undocumented components, suggest: "Component patterns table is behind. Consider running `/super:design` to update it." Same for direction: if the current change conflicts with the documented direction in multiple ways, suggest `/super:redesign` for the affected screen.

One line per observation. Only when relevant. Not blocking. Brief.

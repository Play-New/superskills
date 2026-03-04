---
name: design-awareness
description: Design system awareness during planning. Fires when Claude enters plan mode or proposes UI changes. Checks token compliance, component reuse, and aesthetic consistency.
user-invocable: false
---

When planning changes to UI code:

1. If `.superskills/design-system.md` does not exist, stop. No design system configured.
2. Read `.superskills/design-system.md` for direction, tokens, and component patterns. Read CLAUDE.md for the Design System section.
3. **Modality check:** if `.superskills/design-system.md` contains an EIID Interface Map, check: does this UI change target a layer mapped to visual modality? If adding a visual component for a layer mapped to conversational or notification delivery, flag: "This layer is mapped to [modality] delivery. Confirm a visual surface is needed before proceeding."
4. **Channel design check:** if the change involves message templates, notification formatting, or chat responses, and `.superskills/design-system.md` contains an EIID Interface Map, check against it. Does the message structure match documented patterns? Does the message lead with insight? Is terminology consistent with visual surfaces?
5. **Component discovery:** before building any custom component, search for existing implementations. Run `npx shadcn@latest add` to explore available registries and components. Check the shadcn ecosystem: base registry, community registries (@reui, @animate-ui, @diceui, and others). If a component exists, install it instead of building custom.
6. **Direction check:** does this change match the established aesthetic direction? Flag drift from the documented feel, color world, or signature element.
7. **Pattern reuse:** if `.superskills/design-system.md` has documented measurements (heights, padding, radius), use them. Do not create new variants when an existing pattern fits.
8. **Hierarchy check:** if `.superskills/design-system.md` has an Information Architecture section, check three things. Navigation budget: if the change adds a nav item, count existing items against the budget; exceeding means something moves down or the new item nests inside an existing screen. Focal point: if the change adds content to a screen's primary zone, check if it competes with the documented focal point. Content depth: if the change surfaces something that belongs at one-click or deep tier based on documented rules, flag it.
9. **Layout compliance:** if `.superskills/design-system.md` has a Layout section, check that new components use documented grid, breakpoints, and page patterns. Flag components that bypass the grid or use undocumented breakpoints.
10. **Typography compliance:** if `.superskills/design-system.md` has a Typography Scale section, check that new text elements use documented levels. Flag sizes or weights not in the scale.
11. **Composition compliance:** if `.superskills/design-system.md` has a Composition section, check density zones and section rhythm. Flag uniform spacing where the density map says it should vary. Flag spacing values that don't match the documented section rhythm.
12. **Staleness nudge:** if `.superskills/design-system.md` has a Component Patterns table, count how many UI components exist in the codebase vs. how many are documented in the table. If the gap is 5+ undocumented components, suggest: "Component patterns table is behind. Consider running `/super:design` to update it." Same for direction: if the current change conflicts with the documented direction in multiple ways, suggest `/super:design [screen name]` to redesign the affected screen.

One line per observation. Only when relevant. Not blocking. Brief.

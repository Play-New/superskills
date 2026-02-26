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

One line per observation. Only when relevant. Not blocking. Brief.

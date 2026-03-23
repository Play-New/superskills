---
name: design-awareness
description: Design awareness during planning. Challenges whether UI changes should exist at all, then checks direction, hierarchy, and system compliance.
user-invocable: false
---

When planning changes to UI code:

1. If `.superskills/design-system.md` does not exist, stop. No design system configured.
2. Read `.superskills/design-system.md` for direction, tokens, and component patterns. Read CLAUDE.md for the Design System section.

3. **Should this exist?** Before checking how it looks — should this screen, component, or element exist at all? Does it serve the EIID mapping? Is there a simpler way to deliver this value? Can the information be part of an existing screen instead of a new one? Can the interaction be conversational instead of visual? Flag additions that don't earn their place.

4. **Modality check:** if `.superskills/design-system.md` contains an EIID Interface Map, check: does this UI change target a layer mapped to visual modality? If adding a visual component for a layer mapped to conversational or notification delivery, flag: "This layer is mapped to [modality] delivery. Confirm a visual surface is needed before proceeding."

5. **Channel design check:** if the change involves message templates, notification formatting, or chat responses, check against the EIID Interface Map. Does the message structure match documented patterns? Does the message lead with insight? Is terminology consistent with visual surfaces?

5b. **Agent interaction check:** if the change involves agent-generated output visible to users, check: is the agent's tone consistent with the design direction? Does the agent communicate what it's doing (transparency)? Are error messages actionable, not raw?

6. **Component discovery:** before building any custom component, search for existing implementations. Check shadcn registries and community registries. If a component exists, install it instead of building custom.

7. **Direction check:** does this change carry the product's character? Not "are the colors right" — does it feel like this product? A fleet dashboard component should feel dense and operational. A recipe component should feel warm and generous. Flag output that feels generic or templated.

8. **Hierarchy check:** if an IA section exists, check navigation budget (exceeding means something moves down), focal point (new content shouldn't compete with the documented focal), content depth (surface-tier items stay on surface, deep items stay deep).

9. **System compliance:** tokens, layout grid, typography scale, composition rhythm. Use documented values, not arbitrary ones. Don't create new variants when existing patterns fit.

10. **Staleness nudge:** if 5+ undocumented components exist vs. the Component Patterns table, suggest `/super:design` to update. If multiple direction conflicts, suggest `/super:design [screen]` to redesign.

One line per observation. Only when relevant. Not blocking. Challenge, don't lecture.

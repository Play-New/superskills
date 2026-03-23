# RecipeBox — Build Plan

Mixed modality consumer product. Agents + workflows. WhatsApp is primary. Web is archive.

## What This Product Needs (derived from strategy)

- **Auth:** yes — users have saved recipes and preferences
- **Schema:** yes — users, recipes, ingredients, pantry_items, collections. Minimal: what the product actually queries.
- **Layout shell:** yes, but minimal — top bar, 3 items, 640px constrained. This is not a dashboard.
- **Settings:** yes — dietary preferences, notification timing, prompt management (the agent's behavior is shaped by prompts the user should see and adjust).
- **Visual surfaces:** yes, but secondary — the web archive is for browsing saved recipes, not primary interaction.
- **Agent runtime:** yes — the conversational recipe agent IS the product. WhatsApp-native.

## What This Product Does NOT Need

- Recipe creation UI. Users don't create recipes — the system matches them.
- Complex filtering on the web. Three ways to browse: recent, collections, search. That's it.
- Admin panel. Solo developer product. No roles, no multi-tenancy.
- Real-time updates on the web. The web is an archive. Static read.
- Notification settings per channel. WhatsApp is primary, email is weekly summary. Fixed.

## Build Order

1. **Scaffold + schema** — users, recipes, ingredients, pantry_items, collections. Lean. No table for "categories" or "tags" — the agent classifies on the fly.
2. **Auth** — Supabase Auth. Simple email login. The web archive is behind auth. WhatsApp is connected to the user account.
3. **Enrichment** — WhatsApp webhook (photo intake), OCR receipt parsing, pantry image recognition. The entry point of the whole product.
4. **Inference** — recipe matching workflow: parse receipt → extract ingredients → score against recipe DB. Fixed sequence, LLM in individual nodes.
5. **Agent** — tools first: get_recipe, search_pantry, update_preference, save_recipe, get_substitution. Then the orchestration. Prompts in dedicated files, not inline. Follows agent interaction patterns from design system.
6. **Delivery** — WhatsApp recipe cards + confirmations (agent handles this). Email weekly summary via Resend.
7. **Layout + web archive** — top bar (Recipes, Collections, Settings). Single column, 640px. Recipe cards with the availability badge (signature). Warm, generous, editorial.
8. **Settings** — dietary preferences, connected accounts, prompt inspector. The user can see what the agent is instructed to do and adjust cuisine preferences, dietary constraints, tone.

## Key Design Decisions in Build

- The availability badge ("8/10 ingredients") is built with the recipe card component, not added later. It's the signature.
- 18px body text. Users read at arm's length in a kitchen. This is a design decision from the design system, embedded in the type scale from the first component.
- No multi-column layouts. Recipe reading is linear. The design system says single column, constrained.
- The agent's tone matches the design direction: warm, specific, helpful. Not terse, not chatty. "Based on your pantry, Thai works best" — clarification with default, from the agent interaction patterns.
- Prompts are in `/prompts/` directory, each with a comment explaining its purpose. Settings UI shows a readable version.

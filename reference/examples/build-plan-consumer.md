# RecipeBox — Build Plan

Example build plan produced by `/super:build` init mode for a mixed-modality consumer product. Uses agents and workflows with graduation triggers. WhatsApp is the primary interface — the web archive is secondary.

## Readiness

- **Strategy gate:** PASS — CLAUDE.md has EIID mapping with Approach fields on all layers
- **Stack gate:** PASS — Next.js, Supabase, Resend, WhatsApp Business API installed
- **Design gate:** PASS — design-system.md has Direction (warm kitchen table), Typography (serif display + sans body at 18px), Tokens (stone palette, terracotta accent), Conversational Patterns, Agent Interaction Patterns

## Build Order

### Shared Standards

| # | Piece | Type | Dependencies |
|---|-------|------|--------------|
| 1 | Project scaffold | shared-standard | none |
| 2 | Database schema — users, recipes, ingredients, collections, pantry_items, preferences | shared-standard | #1 |
| 3 | Auth — Supabase Auth, middleware, protected routes | shared-standard | #1 |
| 4 | Layout shell — top bar (Recipes, Collections, Settings), 640px constrained | shared-standard | #3 |
| 5 | Settings — dietary preferences, notification timing, WhatsApp connection, prompt management | shared-standard | #3, #4 |
| 6 | Error handling — structured API errors, agent error patterns (photo too dark → recovery) | shared-standard | #1 |
| 7 | Logging — structured JSON, agent action logging | shared-standard | #1 |

### EIID Layers

| # | Piece | Type | EIID Layer | Approach | Dependencies |
|---|-------|------|------------|----------|--------------|
| 8 | WhatsApp webhook — receive photos, parse media, respond with confirmation | unique | Enrichment | Automate via buy (WhatsApp API + OCR) | #2, #7 |
| 9 | Receipt OCR — extract ingredients from receipt photos | unique | Enrichment | Automate via buy (OCR service) | #8 |
| 10 | Pantry recognition — identify ingredients from pantry photos | unique | Enrichment | Automate via buy (image recognition) | #8 |
| 11 | Recipe matching — ingredient availability → recipe scoring | unique | Inference | Automate via workflow | #2, #9, #10 |
| 12 | Recipe agent — conversational adaptation, substitutions, dietary adjustments | unique | Interpretation | Differentiate via agent | #11, #5 |
| 13 | WhatsApp delivery — recipe cards, confirmations, weekly summary trigger | unique | Delivery | Innovate via agent | #12 |
| 14 | Email weekly summary — Resend template, top 3 recipes, meal plan link | unique | Delivery | Automate via code | #12 |

### Agent Runtime

| # | Piece | Type | Dependencies |
|---|-------|------|--------------|
| 15 | Agent tools — get_recipe, search_pantry, update_preference, save_recipe, get_substitution | unique | #2, #11 |
| 16 | Agent orchestration — single agent with tools, WhatsApp channel, Supabase state | unique | #12, #13, #15 |
| 17 | Prompt management — system prompts visible in settings, user overrides, versioning | unique | #5, #16 |

### Visual Surfaces (secondary — web archive)

| # | Piece | Type | Screen | Dependencies |
|---|-------|------|--------|--------------|
| 18 | Recipes list — saved recipe cards with photo, title, prep time, availability badge | unique | Recipes | #4, #2 |
| 19 | Recipe detail — photo, ingredients, availability match, step-by-step instructions | unique | Recipe Detail | #18 |
| 20 | Collections — user-organized groups, collection thumbnails | unique | Collections | #4, #2 |

### Background Jobs

| # | Piece | Type | Dependencies |
|---|-------|------|--------------|
| 21 | Weekly meal plan generation — Sunday 17:00, top 3 recipes based on pantry | unique | #11, #14 |

## Acceptance Criteria (examples)

**#5 Settings (with prompt management):**
- Settings page shows dietary preferences, notification timing, connected accounts
- Prompt inspector shows the recipe matching prompt and the agent system prompt in readable format
- User can edit the agent's cuisine preference prompt, save override, reset to default
- When system prompt is updated via deploy, user override is preserved

**#12 Recipe agent:**
- Given parsed ingredients [chicken, rice, coconut milk], agent suggests Thai green curry with availability match
- User says "make it spicier" → agent adjusts recipe, re-sends card
- User says "I don't have yogurt" → agent suggests substitution (coconut cream) and recalculates
- Agent follows transparency pattern: "Checking your pantry... found 12 items. Matching recipes..."
- Agent follows clarification pattern: "Thai or Japanese? Based on your pantry, Thai works best."
- Agent follows handoff pattern: "Full comparison with nutrition: [web link]"

**#15 Agent tools (parity check):**
- get_recipe: same data as web recipe detail page
- search_pantry: same search as web recipe filter
- update_preference: same as settings dietary preferences
- save_recipe: same as web save button
- Every web UI action has a corresponding tool ✓

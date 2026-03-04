# Design System

## Direction
**Feel:** Warm kitchen table. The product should feel like a knowledgeable friend who cooks, not a database of recipes.
**Domain concepts:** Pantry, ingredient, meal plan, prep time, grocery receipt, collection, availability match
**Color world:** Cream linen, terracotta clay, olive oil, toasted bread, fresh herb green. The palette of a weekend farmers market.
**Signature:** The availability percentage on every recipe card. "You have 8 of 10 ingredients" tells you this recipe is within reach. No other recipe tool leads with what you already own.
**Rejected defaults:** Recipe aggregator grid with ad sidebars, clinical white backgrounds, stock food photography with studio lighting

## EIID Interface Map

| Layer | Modality | Surface | Rationale |
|-------|----------|---------|-----------|
| Enrichment | conversational | WhatsApp photo message | Users already photograph groceries. Meet them there. |
| Inference | none | internal pipeline | No user-facing controls needed. Scoring is automatic. |
| Interpretation | mixed | WhatsApp card + web detail | Quick answer in chat, full recipe on web when cooking. |
| Delivery | conversational | WhatsApp + email + web archive | WhatsApp for real-time, email for planning, web for reference. |

**Visual scope:** Interpretation layer needs a web surface for full recipe view and saved collection browsing. Settings page for dietary preferences and notification timing. These are the only screens to design.
**Non-visual scope:** Enrichment is entirely conversational (photo input). Delivery is primarily conversational. The web archive is a complement, not the primary interface. Design implications: the web experience serves reference and browsing, not primary interaction.

## References
**Borrowed:** Editorial recipe layouts from Ottolenghi's site (generous photography, serif titles, single-column flow). Warm palette from Momofuku's branding (cream, terracotta, earth tones). Card simplicity from Paprika app (title + time + one-line summary).
**Avoided:** Pinterest-style masonry grid (too dense, too much choice). AllRecipes ad-heavy layout (cluttered, hostile to scanning). Blue-tinted clinical food photography (cold, institutional).
**Assets:** None. No existing brand materials.

## Information Architecture
**Navigation:** Top bar, minimal. Three items: Recipes (saved collection), Collections (user-organized groups), Settings (dietary preferences, notification timing, connected accounts).
**Excluded from nav:** Individual recipe view (drill-down from Recipes). Onboarding (one-time flow). WhatsApp connection (inside Settings).

Most interaction happens in WhatsApp. The web archive exists for moments when users want to browse saved recipes, organize collections, or adjust preferences. Navigation reflects this: three destinations, no hierarchy.

| Screen | Focal Point | Above the Fold | One Click | Deep |
|--------|-------------|----------------|-----------|------|
| Recipes | Saved recipe count | Recent recipes (3-4 cards), search bar | Full recipe list with filters (cuisine, prep time, dietary) | Recipe detail view |
| Recipe Detail | Recipe photo + title | Photo, ingredient list, availability match, prep time | Step-by-step instructions, nutrition info | Source attribution, similar recipes |
| Collections | Collection grid | User-created collections (meal prep, weeknight, guest dinner) | Collection contents | Edit collection, reorder |
| Settings | Dietary preferences | Dietary restrictions, notification timing | Connected accounts (WhatsApp, email) | Data export, account deletion |

**Content depth rules:**
- Surface: recipe cards with photo, title, prep time, availability match. The information needed to decide "should I cook this tonight?"
- One click: full recipe with instructions, nutrition, ingredient substitutions
- Deep: settings, collection management, account configuration

## Layout
**Grid:** Single column, constrained. 640px max-width for recipe content. No multi-column layouts.
**Breakpoints:** sm 640px (only breakpoint that matters). Above 640px, content is centered with generous side margins.
**Container:** 640px max-width, centered. 16px edge padding on mobile. Content never stretches wide.
**Page patterns:**

| Pattern | Structure | Used on |
|---------|-----------|---------|
| Card list | Stacked cards, 16px gap, full container width | Recipes, Collections |
| Single column content | Constrained reading width, generous vertical spacing | Recipe Detail |
| Simple form | Centered, 480px max, grouped sections | Settings |

## Typography Scale

| Level | Size | Weight | Line Height | Tracking | Usage |
|-------|------|--------|-------------|----------|-------|
| Display | 32px | 600 | 1.15 | -0.02em | Recipe title on detail page |
| H1 | 24px | 600 | 1.2 | -0.01em | Page titles (Recipes, Collections) |
| H2 | 20px | 500 | 1.3 | 0 | Section headings (Ingredients, Steps) |
| Body | 18px | 400 | 1.6 | 0 | Recipe instructions, descriptions |
| Small | 15px | 400 | 1.5 | 0 | Metadata (prep time, servings, calories) |
| Caption | 13px | 500 | 1.3 | 0.01em | Labels, timestamps, source attribution |

Serif display font for recipe titles (warm, editorial character). Sans-serif for body text and metadata (readable at cooking distance). Larger base size than typical apps: 18px body accounts for glancing at a phone propped on a kitchen counter.

## Composition
**Hierarchy:** Display weight (600) and size (32px) for recipe titles. Medium weight (500) and size (20px) for section breaks. Regular weight (400) and size (18px) for reading content. Hierarchy reads through size alone, no color dependency.
**Density map:** Recipe cards are medium density (16px padding, comfortable scanning). Recipe detail is generous (32px between sections, 24px between ingredient groups). Settings are compact (12px between fields, 24px between groups).
**Section rhythm:** 40px between major sections, 24px between groups within a section, 12px between items within a group.
**Proportion:** Recipe photo is 2:3 ratio, fills container width. Title and metadata sit below the photo at 60% of photo visual weight. Ingredient list and instructions are equal weight to each other.
**Whitespace:** 16px page margins on mobile, expanding to generous side margins on desktop (content stays at 640px). Recipe detail pages breathe: 40px above and below each major section. The web archive should feel unhurried.

## Tokens
**Spacing base:** 4px
**Scale:** 4, 8, 12, 16, 24, 32, 40, 48
**Radius:** 8px (cards, images), 4px (inputs, buttons), 16px (collection thumbnails)
**Depth strategy:** Subtle shadows for cards (recipe cards lift slightly). No borders except on inputs. Shadows are warm-toned, not neutral gray.
**Color primitives:**
- Foreground: stone-900 (primary text), stone-600 (secondary text), stone-400 (muted labels)
- Surfaces: cream/stone-50 (base background), white (cards), stone-100 (settings panels)
- Accent: terracotta-500 (primary actions, availability highlight), olive-600 (success, dietary match), amber-500 (warnings, expiring ingredients)
- Photography: food photos provide the dominant color energy. Interface colors stay muted to let photography lead.

## Component Patterns

| Component | Height | Padding | Radius | Notes |
|-----------|--------|---------|--------|-------|
| Recipe card | auto | 16px | 8px | Photo top, content below. Subtle warm shadow. |
| Button (primary) | 44px | 12px 20px | 8px | Terracotta background. Larger than typical (thumb-friendly). |
| Button (secondary) | 44px | 12px 20px | 8px | Stone-200 background, stone-700 text. |
| Input | 44px | 12px 16px | 4px | 1px stone-300 border. Larger touch target. |
| Collection thumbnail | 120px | 0 | 16px | Photo with overlay text. Rounded for warmth. |
| Availability badge | 24px | 4px 8px | 4px | "8/10 ingredients" in olive or terracotta depending on match. |

## Conversational Patterns

These patterns define how RecipeBox communicates outside the web interface:

**Recipe card (WhatsApp):**
Recipe photo + title + prep time + ingredient match percentage + "Reply SAVE to keep"

**Confirmation (WhatsApp):**
"Got it. 12 items from your receipt. 3 recipes ready. Send SHOW to see them."

**Weekly summary (email):**
Header with week stats (recipes cooked, new ingredients logged), top 3 suggested recipes for the coming week, link to full meal plan on web archive.

## Decisions
| Decision | Why | Date |
|----------|-----|------|
| Serif display font | Editorial warmth. Sans-serif recipe titles look like a database. Serif says "someone curated this." | Mar 2026 |
| 18px body text | Users read recipes on a phone propped on a kitchen counter, at arm's length. Standard 14-16px requires squinting. | Mar 2026 |
| Single column only | Recipe reading is linear: ingredients then steps. Multi-column layouts fragment the cooking flow. | Mar 2026 |
| Warm shadows over borders | Borders feel clinical (FleetPulse uses them deliberately). Warm-toned shadows feel like paper sitting on a wooden table. | Mar 2026 |

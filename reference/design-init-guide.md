# Design Init Guide

Detailed process for each step of `/super:design` init mode. The command file defines the flow. This file defines the execution.

## Interface Modality Assessment

Read the EIID mapping from CLAUDE.md. For each layer, determine the interface modality:

**Enrichment:** How does data enter the system? If users contribute through existing channels (chat messages, email, photos, voice), the design problem is input format and confirmation, not web forms. If enrichment requires manual configuration (API keys, polling rules, source selection), that configuration needs a visual settings surface.

**Inference:** Where do patterns surface? If inference is an internal pipeline with no user-facing controls, no interface is needed. If users configure rules, thresholds, or models, that configuration needs a visual surface. If inference produces real-time status that users monitor, that needs a visual dashboard.

**Interpretation:** How do users consume insights? Self-contained statements that fit in a message (a number, a verdict, a recommendation) work conversationally. Spatial understanding (charts, maps, timelines, multi-variable comparison) requires a visual surface. Most products have both: headline conversational, detail visual.

**Delivery:** List every output channel. For each: input-only, output-only, or bidirectional? What format constraints does the channel impose?

Produce the EIID Interface Map table (see `reference/design-system-template.md`).

**Routing:**
- If ALL layers are conversational, notification, or embedded: no graphical interface is needed. Skip visual design steps entirely. Write a design system covering message structure and channel formatting only. This is a valid, complete outcome.
- If SOME layers need a visual surface: proceed with visual design steps, scoped to those layers only.
- If ALL layers need a visual surface: proceed with the full visual design process.

## Explore the Product's World

> This section applies to layers mapped to a visual modality. If no layers are visual, skip to writing the design system.

Read CLAUDE.md for business context and EIID mapping. Before choosing any visual direction, explore:

**Who opens this interface?** Not "users" — the actual person. Where are they? What's on their mind? A teacher at 7am with coffee is not a developer debugging at midnight.

**What must they accomplish?** The verb. Grade submissions. Find the broken deployment. Approve the payment.

**What should this feel like?** Say it in words that mean something. "Clean and modern" means nothing. Warm like a notebook? Cold like a terminal? Dense like a trading floor? Calm like a reading app?

Then produce:

- **Domain concepts:** 5+ metaphors and vocabulary from this product's world. Not features — territory.
- **Color world:** 5+ colors that exist naturally in this product's domain. If this were a physical space, what would you see?
- **Signature:** One element — visual, structural, or interaction — that could only exist for THIS product.
- **Defaults to reject:** 3 obvious choices you will NOT use. Name them explicitly so you can avoid them.

Also consider:

- **Spatial composition:** asymmetry, overlap, density variation, grid-breaking as intentional choices. Not every layout sits on a centered 12-column grid.
- **Atmosphere:** backgrounds are not solid colors. Gradient, grain, texture that matches the direction. A reading app's warm paper texture is not a trading floor's dark matte surface.
- **Anti-convergence:** if another AI given similar prompts would produce substantially identical output, the design has failed. What makes this product visually distinct from a default template?

Do not propose a direction until all four items above are produced.

## Direction Assessment

Present findings from the exploration to the user. Then collect input in a single block. The user answers what they want, skips what they don't.

**References:**
"Any visual references? URLs of sites you admire, screenshots, Figma files, brand names, designers, or design studios. Even from different domains, as long as something about them feels right for this product."

For each reference provided:
- URLs: fetch the page, analyze layout density, palette, typography, spacing rhythm, atmosphere, navigation pattern
- Screenshots/images: view the image, same analysis
- Figma URLs: get design context via Figma tools, same analysis
- Brand names: search for the brand's visual identity, then analyze
- Designers or design studios: research their body of work, philosophy, signature patterns, and decision-making principles. Apply their perspective as a lens throughout the design process. Not "copy their style" but "how would they approach this problem for this product."

**Anti-references:**
"Anything you've seen and actively don't want? Sites, styles, or patterns that would be wrong here."

**Existing assets:**
"Any brand material? Logo, colors, fonts, illustration style, tone guide. Even a rough sketch."

For existing assets: analyze and incorporate as constraints. Brand colors become the palette foundation. Brand fonts skip the typography selection step. A logo's character informs the direction.

**Direction check:**
"The exploration produced this direction: [summary]. Does this match? Anything to push further, pull back, or change entirely?"

**Constraints:**
"Hard constraints? Required colors, mandated fonts, accessibility targets beyond AA, platform requirements."

Process all inputs. For each reference and anti-reference, extract concrete patterns:
- What to borrow: specific and named ("the tight sidebar density from Linear", "the amber-on-dark palette from that dashboard screenshot")
- What to avoid: specific and named ("the generic card grid from that SaaS site", "the low-contrast gray text")

Produce a reference summary: 3-7 distilled patterns across all references. Not "modern and clean" but "tight 8px grid, monochrome surfaces, single accent color for CTAs, generous whitespace between sections."

Revise the direction based on assessment answers. If the user provided nothing, proceed with the exploration direction as-is.

Store the reference summary in `.superskills/design-system.md` under a References section.

## Style Direction

For shadcn projects, run `npx shadcn@latest init` to see available styles. Pick the one that matches the product's character. The style determines spacing rhythm, border radius, and component density. Present the recommendation to the user with reasoning.

Three dimensions to decide:
- **Density:** tight (data-heavy, admin) vs. generous (consumer, editorial)
- **Shape:** sharp/boxy (technical) vs. rounded/pill (approachable)
- **Weight:** compact (efficiency-first) vs. spacious (comfort-first)

For non-shadcn projects, skip this step — the equivalent decisions are encoded directly in the theme file.

## Typography Scale

Ask the user if they have brand fonts first. If they do, use those.

If not, pick a display font + body font pairing that matches the direction. Read `reference/design-craft.md` for the Typography Character section. Search for fonts that match the direction. Browse foundries (Google Fonts, Fontshare, Atipo, fonts.bunny.net) for options. Choose based on the product's character, not popularity.

**The default test:** if the font is what the framework installs by default, or what most AI-generated projects use, it's not a design decision. Choose intentionally. A fleet management dashboard and a children's reading app should not share the same typeface.

Pick two: one display font (headings, hero text) and one body font (paragraphs, UI labels). They should contrast in character but share similar x-height.

If references included typography patterns, use them as starting points.

Then expand from font choice to a full type scale. Define every level from Display through Mono: size, weight, line height, tracking, and usage. The scale should produce three clearly distinguishable hierarchy tiers (primary, secondary, tertiary) through weight and size alone. Apply the hierarchy test: strip color from the page. If primary, secondary, and tertiary content are still visually distinct, the scale works. If hierarchy collapses without color, add more size or weight contrast between levels.

## Layout Architecture

Define the spatial structure of the product. Decisions flow from IA: data tables need full width, forms need constraints, dashboards need flexibility.

**Grid:** column count, gutter size, system. Not every product needs 12 columns. A text-heavy tool might use 8. A dashboard might use a flexible grid with named areas.

**Breakpoints:** define the breakpoints where layout changes. Include pixel values and what shifts at each.

**Container:** max-width strategy. Some screens (dashboards, tables) should be fluid. Others (forms, settings, long text) need constraints to stay readable. Define both.

**Page patterns:** document the 3-5 recurring layout structures. Sidebar + content, full-width table, split panel, constrained form, card grid. Each pattern maps to specific screen types from the IA.

## Composition Rules

Define the structural relationships that make the interface feel composed rather than assembled. These five decisions sit between layout (where things go) and tokens (how things look).

**Hierarchy:** how primary, secondary, and tertiary content are distinguished. Size, weight, color, space, and position all contribute. Document the specific combination. Must produce three clearly distinguishable levels that work without color.

**Density map:** not every zone of the interface should feel the same. Tables are dense. Key metrics get breathing room. Settings are medium. Document which zones are dense, which are generous, and why. The density should match the task: scanning 30 items needs density, understanding one metric needs space.

**Section rhythm:** three numbers. Spacing between major sections, between groups within a section, between items within a group. These three values create a visual cadence across every screen. Consistent rhythm is what makes an interface feel designed.

**Proportion:** the size relationship between the focal point and supporting content. If the focal element is the same size as everything else, there is no focal point. Define the ratio.

**Whitespace:** page margins, breathing room around focal elements, grouping gaps vs separating gaps. Grouping gaps must be visibly smaller than separating gaps. This is how spatial composition communicates structure without borders.

## Token Generation

All aesthetic choices become design tokens. No creative decision lives in component code.

**Extract before propose:** if existing UI code is present, discover the system already embedded in the code before proposing anything new.

1. Scan all .tsx/.jsx/.vue/.svelte files for repeated CSS values: padding, margin, gap, border-radius, height, font-size, color, box-shadow
2. Count occurrences. Group by property. Sort by frequency.
3. Present findings: "36px height used in 7 of 8 buttons. 16px padding in 12 components. border-radius 8px in 9 places, 4px in 3."
4. Propose a token set that formalizes the most common values. Flag outliers as candidates for consolidation.
5. Ask the user to confirm before writing tokens. The existing code is evidence. Respect it.

If no existing UI code is found, skip extraction and propose tokens from the direction.

**For shadcn + Tailwind:**
- Run `npx shadcn@latest create` with the chosen style, or generate `globals.css` manually
- CSS custom properties in OKLCH (Tailwind v4) or HSL. Both `:root` and `.dark`
- Variables for: colors (background, foreground, card, primary, secondary, accent, muted, destructive, border, ring), `--radius`, `--font-display`, `--font-body`, spacing scale, shadow scale, transition durations
- Token names should evoke the product's world — not just `--gray-700` but names that someone reading only the tokens could guess what product this is
- Set up cn() utility: `npm install clsx tailwind-merge`
- Initialize: `npx shadcn init`
- Add base components: `npx shadcn add button card input label badge dialog dropdown-menu table`

**For Chakra/MUI/Mantine:**
- Create theme.ts with full token set: colors, fonts (display + body), spacing, radii, shadows
- Wrap app in the framework's provider

**For Tailwind only:**
- Create globals.css with the same CSS variable set
- Configure tailwind.config.ts extending from variables

**Key rule:** the token file IS the aesthetic. Strip all components and rebuild from tokens alone — the product should still look the same.

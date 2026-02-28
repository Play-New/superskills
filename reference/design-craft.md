# Design Craft

How to execute intentional interfaces. Not what to build, but how to build it well.

Every decision here traces back to strategy. The EIID mapping determines what needs attention. The user context determines density, tone, and pace. Craft without strategy produces beautiful interfaces nobody needs. Strategy without craft produces useful interfaces nobody wants to use.

Read CLAUDE.md and `.superskills/design-system.md` before applying anything below. The direction chosen in `/super:design` determines which techniques apply and how far to push them.

## Direction Spectrum

Not every product needs the same treatment. The direction from `/super:design` places the product somewhere on these axes:

- **Density:** generous space (editorial, consumer) to packed information (admin, trading, ops dashboard). The user's task frequency and data volume determine this, not preference.
- **Temperature:** warm and organic (grain, rounded shapes, natural colors) to cold and precise (sharp edges, monospace, high contrast). The user's emotional context determines this.
- **Energy:** calm and restrained (reading, planning, reflection) to active and dynamic (monitoring, creating, responding). The task urgency determines this.
- **Complexity:** minimal surface (few elements, deep navigation) to maximal surface (many elements, shallow navigation). The information architecture determines this.

These are spectrums, not categories. Most products sit somewhere in the middle, leaning one direction. The point is to lean intentionally.

## Spatial Composition

Layout communicates before content does. Where things sit, how much space they get, and what breaks the grid tells the user what matters.

- **Density variation:** not uniform spacing everywhere. Dense areas (data tables, metrics) signal "scan this." Open areas (hero sections, key actions) signal "focus here." The contrast between dense and open creates rhythm.
- **Asymmetry:** centered, equal-column layouts are the default output of every template. Intentional asymmetry (wider left panel, offset hero, unequal grid columns) creates visual interest and hierarchy. Use it where the content justifies it.
- **Overlap and layering:** elements can overlap, cast shadows on each other, break out of their containers. A card that bleeds past its section boundary draws attention. Use sparingly, at focal points.
- **Grid-breaking:** the grid is structure, not prison. Elements that break the grid (a full-bleed image in a contained layout, a floating action that sits outside the content area) create moments of visual tension. One per screen maximum.
- **Negative space as element:** empty space is not "unused." Large margins around a key metric say "this is important." Tight margins between related items say "these belong together." Space groups and separates.

Spatial decisions follow the information architecture. Focal points from the IA get the most spatial weight. Surface-tier content gets the best real estate. Deep-tier content gets compact, efficient treatment.

## Typography Character

Type is the first thing users read and the last thing designers think about. The font choice is the single biggest signal of whether a product was designed or generated.

**The default test:** if the font ships with the framework, or appears on 50% of AI-generated sites, it is not a design decision. Inter, Geist, system-ui, and Roboto are fine fonts. They are also invisible. They say "nobody chose this."

**Selection by character, not popularity:**
- What does this product sound like? A children's app doesn't speak in the same voice as a compliance dashboard.
- Browse foundries (Google Fonts, Fontshare, Atipo, fonts.bunny.net) with the product's character in mind.
- A display font for headings and hero text: this carries the personality.
- A body font for paragraphs and UI: this carries the readability.
- They should contrast in character but share similar x-height and optical size.

**Type as structure:**
- Typography hierarchy should work without color. If removing all color leaves the hierarchy intact (through size, weight, and spacing alone), the type system works.
- Three levels minimum: heading, body, caption. Most products need five: display, heading, body, label, caption.
- Line heights: tighter for headings (1.1-1.2), generous for body (1.5-1.6), compact for labels (1.2-1.3).
- Letter spacing: slightly tracked for all-caps labels (+0.05em), tight for large display text (-0.02em), default for body.

**Monospace for data:** numbers that need alignment (prices, metrics, IDs, timestamps) benefit from monospace or tabular figures. Mixing proportional body text with monospace data creates a natural visual separation between narrative and numbers.

## Visual Identity

Anti-convergence is not a style. It is a test: if another AI given similar prompts would produce substantially identical output, the design has failed.

**Signature element:** one visual, structural, or interaction choice that could only exist for THIS product. Not a logo. Something in the interface itself. FleetPulse's amber attention count. A reading app's page-turn animation. A music tool's waveform navigation. The signature should emerge from the product's domain, not be forced.

**Committed direction:** half-measures produce generic results. A warm, organic direction means grain textures, rounded shapes, natural spacing, handwritten accents. Not one rounded corner on otherwise sharp cards. Commit to the direction or pick a different one.

**Decorative elements with purpose:** borders, dividers, icons, illustrations, patterns. Each should earn its place. A subtle dot-grid background on a technical tool reinforces the precision feel. A gradient wash on a consumer app adds warmth. Decorative elements disconnected from the direction are noise.

**The palette test:** show someone only the color palette (no layout, no type, no content). Can they guess what kind of product this is? If the palette could belong to anything, it is not doing its job.

## Subtle Layering

The goal is surfaces that layer like paper, not like colored blocks.

- **Lightness shifts:** 2-5% between adjacent surface levels. White (100%) to card (98%) to recessed (96%) to inset (93%). The eye registers depth without conscious effort.
- **Border opacity:** use foreground color at 8-15% opacity instead of fixed gray values. `border-color: oklch(0% 0 0 / 0.08)` adapts to any surface and disappears when it's not needed.
- **Shadow progression:** if using shadows, scale by elevation. Level 1: `0 1px 2px oklch(0% 0 0 / 0.05)`. Level 2: `0 2px 4px oklch(0% 0 0 / 0.08)`. Level 3: `0 4px 8px oklch(0% 0 0 / 0.1)`. Same angle, same hue, increasing spread and opacity.
- **Dark mode inversion:** surfaces get lighter as they elevate (opposite of light mode). Background (10%) to card (14%) to raised (18%). Borders become foreground at 10-12% opacity.

Pick one depth strategy (borders-only, subtle-shadows, or layered) and apply it everywhere. Mixing strategies creates visual noise.

## Atmosphere

Backgrounds are environments, not fill colors.

- **Gradient:** radial or linear, same hue family, 2-4% lightness range. A warm app might go from stone-50 center to stone-100 edges. Barely visible, but the screen feels alive.
- **Grain:** CSS noise texture at 3-5% opacity over the base. Adds organic warmth. Works for editorial, warm, handmade directions. Wrong for clinical, technical, data-heavy directions.
- **Texture mapping:** match texture to direction. Paper grain for editorial. Subtle dot grid for technical. Clean gradient for consumer. Nothing for admin (density wins over atmosphere).

Atmosphere is optional. Some directions (precision, density) are better with flat surfaces. The absence of atmosphere is also a choice. Document it.

## Motion

Motion communicates state change, not decoration.

**Duration scale:**
- 100ms: color changes, opacity (instant feedback)
- 150ms: hover states, focus rings (responsive)
- 200ms: button press, toggle, small element transitions (snappy)
- 300ms: modal enter, dropdown open, panel slide (smooth)
- 500ms: page transition, skeleton to content (deliberate)

**Easing:**
- Enter: `cubic-bezier(0, 0, 0.2, 1)` fast start, gentle land
- Exit: `cubic-bezier(0.4, 0, 1, 1)` gentle start, fast disappear
- Move: `cubic-bezier(0.4, 0, 0.2, 1)` smooth both ends

**Orchestration:** one coordinated page load is better than 15 independent animations. Stagger content entry by 30-50ms per element, same direction, same easing. If elements animate independently with different timings, it feels broken.

**Reduced motion:** wrap all animation in `@media (prefers-reduced-motion: no-preference)`. When reduced motion is preferred, replace motion with instant opacity transitions (150ms fade, no transform).

## Color Intent

Every color has a job. Document the job, not just the value.

- **Foreground hierarchy:** primary (text), secondary (labels, metadata), muted (placeholders, disabled). Three levels minimum.
- **Surface hierarchy:** base, card/panel, recessed, inset. Four levels.
- **Semantic:** success (green family), warning (amber family), error (red family), info (blue family). These never change meaning.
- **Interactive:** primary action, secondary action, destructive action. Mapped to semantic colors but used for buttons/links specifically.
- **Data:** chart colors that remain distinguishable in all three forms of color blindness (deuteranopia, protanopia, tritanopia). Use 4-6 colors maximum. Avoid red+green adjacency.

**Rule:** if two elements use the same color for different purposes, one of them needs a new token.

# Design Craft

How to execute subtle, intentional interfaces. Not what to build, but how to build it well.

## Subtle Layering

The goal is surfaces that layer like paper, not like colored blocks.

- **Lightness shifts:** 2-5% between adjacent surface levels. White (100%) → card (98%) → recessed (96%) → inset (93%). The eye registers depth without conscious effort.
- **Border opacity:** use foreground color at 8-15% opacity instead of fixed gray values. `border-color: oklch(0% 0 0 / 0.08)` adapts to any surface and disappears when it's not needed.
- **Shadow progression:** if using shadows, scale by elevation. Level 1: `0 1px 2px oklch(0% 0 0 / 0.05)`. Level 2: `0 2px 4px oklch(0% 0 0 / 0.08)`. Level 3: `0 4px 8px oklch(0% 0 0 / 0.1)`. Same angle, same hue, increasing spread and opacity.
- **Dark mode inversion:** surfaces get lighter as they elevate (opposite of light mode). Background (10%) → card (14%) → raised (18%). Borders become foreground at 10-12% opacity.

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
- Enter: `cubic-bezier(0, 0, 0.2, 1)` — fast start, gentle land
- Exit: `cubic-bezier(0.4, 0, 1, 1)` — gentle start, fast disappear
- Move: `cubic-bezier(0.4, 0, 0.2, 1)` — smooth both ends

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

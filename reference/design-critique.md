# Design Critique

Four layers. Work through each in order. The first build was the draft. The critique is the design.

## 1. Composition

Rhythm, proportion, focal points.

- Does the layout have visual weight hierarchy, or is everything the same size and emphasis?
- Is spacing varied intentionally (dense data area, breathing space around key actions), or uniform everywhere?
- Is there a focal point on each screen? Where does the eye land first? Is that the right place?
- Does the page use the full canvas, or is everything centered in a narrow column by default?

Uniform spacing and equal-weight elements are the signature of undesigned interfaces. Varying density communicates importance.

## 2. Craft

Micro-decisions that separate designed from generated.

- **Surface hierarchy:** are there quiet tonal shifts between levels (2-5% lightness difference), or dramatic jumps? Surfaces should layer like sheets of paper, not stack like colored blocks.
- **Borders:** low-opacity borders (8-15% opacity of foreground color) disappear when not needed and appear when they separate content. Solid 1px borders at full opacity are a default, not a choice.
- **Interactive states:** every clickable element needs hover, focus, active, and disabled states. Not just "darker on hover" but states that feel intentional. A button press should feel like pressing something.
- **Typography layering:** headings, body, captions, labels should each have distinct size, weight, AND color. Three levels of hierarchy minimum. If removing color leaves the hierarchy intact, the typography works.
- **Depth without shadows:** borders-only depth works when border opacity creates the layering. Shadows work when they're consistent (same angle, same blur, scaled by elevation). Mixing both is usually a smell.

## 3. Content

All visible text should form one coherent story.

- Does placeholder data feel real? "John Doe" and "Lorem ipsum" signal unfinished work. Use data that matches the product's domain.
- Do labels, headings, and descriptions speak the same language? A screen mixing formal ("Submit Request") and casual ("Got it!") has no voice.
- Are empty states designed? What does the screen look like with zero data? That's the first thing new users see.
- Do error messages explain what happened AND what to do next?

## 4. Structure

CSS workarounds are smell tests for incomplete thinking.

- Negative margins signal a layout that doesn't fit. Fix the container, not the child.
- `calc()` for basic spacing means the scale is missing a value. Add the token.
- Absolute positioning for layout (not overlays/tooltips) means the flow is broken.
- `!important` means specificity is out of control.
- Magic numbers (padding: 13px, margin: 7px) mean the spacing scale is incomplete.

Every layout problem has a clean solution. Hacks suggest the right structure wasn't found yet.

# Design Critique

Six layers. Work through each in order. The first build was the draft. The critique is the design.

Before starting: read CLAUDE.md for the EIID mapping, user context, and business context. Read `.superskills/design-system.md` for the design direction, information architecture, and tokens. Every observation below is evaluated against what the product is for and who uses it. A beautiful interface that misaligns with the strategy is a failure. A functional interface that ignores its user's context is a failure.

## 0. Strategic Alignment

Before critiquing pixels, check that the design serves the strategy.

- **EIID priority:** interpretation and delivery outputs should dominate the screen. They are where the user gets value. Inference outputs support them (indicators, alerts, scores). Enrichment configuration should be buried. If an enrichment setup screen gets the same visual treatment as a delivery dashboard, the hierarchy is wrong.
- **Focal point alignment:** the element with the most visual weight on each screen should map to the highest-value EIID layer present on that screen. If a settings toggle has more visual presence than the primary insight, the priorities are inverted.
- **User context match:** a fleet manager at 6am scanning 30 vehicles needs density and speed. A teacher reviewing student progress needs warmth and clarity. The design should feel like it was made for the person described in CLAUDE.md, not for a generic "user." Check: does the density match their task complexity? Does the tone match their emotional context? Does the pace match their time pressure?
- **Value chain visibility:** the most valuable parts of the product (what's built, not bought) should be the most prominent. Commodity features (auth, settings, profile) should be visually subordinate. If the login page has more design attention than the core insight screen, effort is misallocated.

If strategic alignment issues exist, they override all other critique layers. Fix alignment first.

## 1. Composition

Rhythm, proportion, focal points.

- Does the layout have visual weight hierarchy, or is everything the same size and emphasis?
- Is spacing varied intentionally (dense data area, breathing space around key actions), or uniform everywhere?
- Is there a focal point on each screen? Where does the eye land first? Is that the right place given the EIID mapping?
- Does the page use the full canvas, or is everything centered in a narrow column by default?
- Does spatial composition support the information architecture? Surface-tier content gets prime real estate. Deep-tier content gets compact treatment.

Uniform spacing and equal-weight elements are the signature of undesigned interfaces. Varying density communicates importance.

### Information Architecture

Five checks for structure before style:

1. **Navigation budget:** Count top-level items. More than 8 means hierarchy was skipped. Check: could any items merge or nest? Are labels specific to this product or could they belong to anything?
2. **Focal point test:** Per screen, identify the single element with most visual weight. If two elements compete (same size, color intensity, position), the screen has no hierarchy.
3. **Primary/secondary/tertiary split:** Primary = focal point + main action. Secondary = supporting data, context. Tertiary = metadata, timestamps, IDs. If everything reads at the same level, the split was never made.
4. **Trade-off test:** For every surface element, ask: would removing this and putting it behind a tab or drawer hurt the core task? If not, it should move. Every visible element costs attention from every other visible element.
5. **Frequency match:** Things the user does every session belong on the surface. Weekly tasks go one click deep. Monthly tasks go behind navigation. If a monthly task sits on the surface, it's stealing attention from daily work.

## 2. Craft

Micro-decisions that separate designed from generated.

- **Surface hierarchy:** are there quiet tonal shifts between levels (2-5% lightness difference), or dramatic jumps? Surfaces should layer like sheets of paper, not stack like colored blocks.
- **Borders:** low-opacity borders (8-15% opacity of foreground color) disappear when not needed and appear when they separate content. Solid 1px borders at full opacity are a default, not a choice.
- **Interactive states:** every clickable element needs hover, focus, active, and disabled states. Not just "darker on hover" but states that feel intentional. A button press should feel like pressing something.
- **Typography layering:** headings, body, captions, labels should each have distinct size, weight, AND color. Three levels of hierarchy minimum. If removing color leaves the hierarchy intact, the typography works.
- **Depth without shadows:** borders-only depth works when border opacity creates the layering. Shadows work when they're consistent (same angle, same blur, scaled by elevation). Mixing both is usually a smell.
- **Spatial intentionality:** is spacing uniform out of convenience or varied out of purpose? Dense grouping for related elements, generous spacing to isolate important ones. The information architecture should be readable from the whitespace alone.

## 3. Content

All visible text should form one coherent story.

- Does placeholder data feel real? "John Doe" and "Lorem ipsum" signal unfinished work. Use data that matches the product's domain.
- Do labels, headings, and descriptions speak the same language? A screen mixing formal ("Submit Request") and casual ("Got it!") has no voice.
- Are empty states designed? What does the screen look like with zero data? That's the first thing new users see.
- Do error messages explain what happened AND what to do next?
- Does the content voice match the user described in CLAUDE.md? A developer tool can be terse. A consumer app should be friendly. A medical tool should be precise. The voice is not separate from the design.

## 4. Structure

CSS workarounds are smell tests for incomplete thinking.

- Negative margins signal a layout that doesn't fit. Fix the container, not the child.
- `calc()` for basic spacing means the scale is missing a value. Add the token.
- Absolute positioning for layout (not overlays/tooltips) means the flow is broken.
- `!important` means specificity is out of control.
- Magic numbers (padding: 13px, margin: 7px) mean the spacing scale is incomplete.

Every layout problem has a clean solution. Hacks suggest the right structure wasn't found yet.

## 5. Identity

The capstone. Run these tests after all other layers are addressed.

**The name test:** describe this interface to someone who cannot see it. Not "a dashboard with a sidebar and cards" but the specific layout, density, color temperature, typography. If the description could apply to any product in the same category, the design lacks identity.

**The swap test:** mentally swap the typeface for a system default. Swap the color palette for a generic blue/gray. Swap the layout for a centered template. Where it wouldn't matter = where the design defaulted. These are the points that need attention.

**The convergence test:** could another AI, given a similar brief, produce something substantially identical? If yes, the design is following the path of least resistance. Identify what is generic and push it toward the product's specific world. The domain concepts, color world, and signature from the design system should be visible in the final interface.

**The screenshot test:** show a screenshot without the logo or product name. Can someone guess what kind of product this is? What domain it serves? If the screenshot could be anything, the visual identity is not doing its job.

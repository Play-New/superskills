# Design Critique

Six layers. Layer 0 (strategy alignment, experience patterns) is the most important and applies to ALL modalities — screens, agents, prompts, notifications, CLI, workflows. Layers 1-5 are primarily visual execution. If layer 0 has issues, they override everything else — a beautifully crafted screen that serves the wrong EIID layer is waste.

Before starting: read CLAUDE.md for the EIID mapping, target feeling, and user context. Read `.superskills/design-system.md` for direction, experience patterns, information architecture, and tokens. Every observation is evaluated against what the product is for, who uses it, and how they should feel. A beautiful interface that misaligns with the strategy is a failure. A functional product that is experientially dead is a failure.

## 0. Strategic Alignment

Before critiquing pixels, check that the design serves the strategy and the target feeling.

- If the design system contains an EIID Interface Map, check whether this screen should exist as visual at all. If content on this screen belongs to a layer mapped to conversational or notification modality, the screen is solving the wrong problem.
- **EIID priority:** interpretation visualizations should dominate the screen — charts, trends, comparisons, spatial views that don't fit in a message. Inference outputs support them (indicators, alerts, scores). The web interface is not the primary input surface: people feed data through their existing channels (chat, email, voice, photos). Configuration (enrichment sources, inference prompts, delivery rules, user management) should be accessible but visually subordinate. If a configuration screen gets the same visual treatment as an interpretation visualization, the hierarchy is wrong.
- **Focal point alignment:** the element with the most visual weight on each screen should map to the highest-value EIID layer present on that screen. If a settings toggle has more visual presence than the primary insight, the priorities are inverted.
- **Experience alignment:** read the target feeling from CLAUDE.md. Does this touchpoint's first impression match? This applies to screens (first 500ms before reading), agent responses (the first line), notifications (the subject line), CLI output (the first line). A touchpoint that is functionally correct but experientially wrong has failed.
- **User context match:** a fleet manager at 6am scanning 30 vehicles needs density and speed. A teacher reviewing student progress needs warmth and clarity. The design should feel like it was made for the person described in CLAUDE.md, not for a generic "user." Check: does the density match their task complexity? Does the tone match their emotional context? Does the pace match their time pressure?
- **Experience pattern compliance:** if `.superskills/design-system.md` has Experience Patterns, check across all modalities: feedback on user actions, appropriate pacing, voice consistency, proportional gratification, restraint applied. Has the absence test been applied? For every element the user perceives across any modality, would the target feeling survive without it? If yes, the element should not exist.
- **Cross-modality coherence:** if the product has multiple touchpoints (dashboard + WhatsApp, CLI + email, agent + notification), do they feel like the same product? Same voice, same terminology, same personality.
- **Value chain visibility:** the most valuable parts of the product (what's built, not bought) should be the most prominent. Commodity features (auth, settings, profile) should be visually subordinate. If the login page has more design attention than the core insight screen, effort is misallocated. If any EIID layer lacks visual representation, confirm whether it needs one before flagging its absence.

If strategic alignment issues exist, they override all other critique layers. Fix alignment first.

## 1. Composition

Rhythm, proportion, focal points. If `.superskills/design-system.md` has Layout, Typography Scale, and Composition sections, use them as the baseline. Deviations from documented layout patterns, type scale levels, density zones, and section rhythm are the first things to check.

- Does the layout follow the documented page patterns and grid? If a Layout section exists, components should use the documented grid, breakpoints, and container strategy. Undocumented patterns are drift.
- Does text use levels from the documented Typography Scale? Sizes or weights outside the scale break the type system.
- Does the layout have visual weight hierarchy, or is everything the same size and emphasis?
- Is spacing varied intentionally (dense data area, breathing space around key actions), or uniform everywhere? If a Composition section documents a density map, check that dense zones are actually dense and generous zones actually breathe.
- Does section spacing match the documented rhythm? Three numbers (major sections / groups / items) should be consistent across screens.
- Is there a focal point on each screen? Where does the eye land first? Is that the right place given the EIID mapping? Does the focal element match the documented proportion?
- Does the page use the full canvas, or is everything centered in a narrow column by default?
- Does spatial composition support the information architecture? Surface-tier content gets prime real estate. Deep-tier content gets compact treatment.

- EIID weight allocation: screens serving interpretation give most of the visual weight to the insight area. Inference screens lead with status indicators. Enrichment and delivery configuration is efficient and dense. If a configuration screen has the same layout generosity as an insight dashboard, the hierarchy reflects the file system, not the value chain.

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

- **Surface hierarchy:** are there quiet tonal shifts between levels, or dramatic jumps? Surfaces should layer like sheets of paper, not stack like colored blocks. The difference should be felt, not counted.
- **Borders:** low-opacity borders using foreground color disappear when not needed and appear when they separate content. Solid borders at full opacity are a default, not a choice.
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
- Cross-channel consistency: if the same insight is delivered both visually and conversationally, language must be consistent. Same terms, same framing, same units across dashboard and chat message. For conversational delivery, content critique applies to message structure: headline, context, action.
- Agent persona consistency: if an agent generates user-facing text, its voice should be coherent across sessions and across UI surfaces. An agent that's casual in chat and formal on the dashboard has no persona.

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

- Channel identity: product identity persists across channels. A WhatsApp message, an email alert, and a dashboard screen should feel like the same product. If they don't, identity is incomplete.
- Agent identity: if an agent interacts with users, its tone and pacing should match the design direction. A warm editorial product should not have a terse robotic agent. A dense operational tool should not have a chatty agent.

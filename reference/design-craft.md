# Design Craft

Execution guidance for intentional interfaces. Craft is the accumulation of small decisions: which font, how much space, what color, how fast. Each one is minor in isolation. Together they determine whether a product feels designed or generated.

Every decision here traces back to strategy. The EIID mapping determines what needs attention. The user context determines density, tone, and pace. Craft serves strategy. Without that connection, a beautiful interface fails just as hard as an ugly one.

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

Type is the first thing users read and the last thing designers think about. The font choice, the scale, the spacing between lines and letters: these are the single biggest signal of whether a product was designed or generated. Typography deserves more time than most teams give it.

**The default test:** if the font ships with the framework, or appears on most AI-generated sites, it is not a design decision. Default fonts are fine fonts. They are also invisible. They say "nobody chose this."

**Selection by character, not popularity:**
- What does this product sound like? A children's app doesn't speak in the same voice as a compliance dashboard.
- Browse foundries (Google Fonts, Fontshare, Atipo, fonts.bunny.net) with the product's character in mind.
- A display font for headings and hero text: this carries the personality.
- A body font for paragraphs and UI: this carries the readability.
- They should contrast in character but share similar x-height and optical size.

**Type as structure:**
- Typography hierarchy should work without color. If removing all color leaves the hierarchy intact (through size, weight, and spacing alone), the type system works.
- Three levels minimum: heading, body, caption. Most products need five: display, heading, body, label, caption.
- Line heights: tighter for headings (density, impact), generous for body (readability), compact for labels (efficiency). The exact values depend on the font's metrics and the direction's density.
- Letter spacing: slightly tracked for all-caps labels (legibility), tight for large display text (cohesion), default for body.

**Monospace for data:** numbers that need alignment (prices, metrics, IDs, timestamps) benefit from monospace or tabular figures. Mixing proportional body text with monospace data creates a natural visual separation between narrative and numbers.

## Visual Identity

Anti-convergence is not a style. It is a test: if another AI given similar prompts would produce substantially identical output, the design has failed.

**Signature element:** one visual, structural, or interaction choice that could only exist for THIS product. Not a logo. Something in the interface itself. FleetPulse's amber attention count. A reading app's page-turn animation. A music tool's waveform navigation. The signature should emerge from the product's domain, not be forced.

**Committed direction:** half-measures produce generic results. A warm, organic direction means grain textures, rounded shapes, natural spacing, handwritten accents. Not one rounded corner on otherwise sharp cards. Commit to the direction or pick a different one.

**Decorative elements with purpose:** borders, dividers, icons, illustrations, patterns. Each should earn its place. A subtle dot-grid background on a technical tool reinforces the precision feel. A gradient wash on a consumer app adds warmth. Decorative elements disconnected from the direction are noise.

**The palette test:** show someone only the color palette (no layout, no type, no content). Can they guess what kind of product this is? If the palette could belong to anything, it is not doing its job.

## Subtle Layering

The goal is surfaces that layer like paper, not like colored blocks. This is one of the most underestimated areas of interface craft. The difference between "flat" and "layered" is often invisible when described but immediately felt when seen.

- **Lightness shifts:** small, consistent steps between adjacent surface levels. The eye should register depth without conscious effort. If the difference reads as a hard edge, it's too much. If adjacent surfaces look identical, it's too little. Test on multiple monitors: what looks subtle on a calibrated display might vanish on a low-contrast laptop screen.
- **Border opacity:** use foreground color at low opacity instead of fixed gray values. The border should disappear when not needed and appear when it separates content. Adapts to any surface automatically.
- **Shadow progression:** if using shadows, scale by elevation. Same angle, same hue, increasing spread and opacity with each level. The progression should feel continuous, not stepped.
- **Dark mode inversion:** surfaces get lighter as they elevate (opposite of light mode). Borders use foreground color at low opacity, same principle as light mode but adjusted for dark backgrounds.

Pick one depth strategy (borders-only, subtle-shadows, or layered) and apply it everywhere. Mixing strategies creates visual noise.

## Atmosphere

Backgrounds are environments, not fill colors.

- **Gradient:** radial or linear, same hue family, narrow lightness range. Barely visible, but the screen feels alive. The gradient should be felt, not seen.
- **Grain:** CSS noise texture at low opacity over the base. Adds organic warmth. Works for editorial, warm, handmade directions. Wrong for clinical, technical, data-heavy directions.
- **Texture mapping:** match texture to direction. Paper grain for editorial. Subtle dot grid for technical. Clean gradient for consumer. Nothing for admin (density wins over atmosphere).

Atmosphere is optional. Some directions (precision, density) are better with flat surfaces. The absence of atmosphere is also a choice. Document it.

## Motion

Motion communicates state change, not decoration. Good motion is invisible. Bad motion is the first thing users notice. The difference between an interface that feels responsive and one that feels sluggish is often a single timing decision.

**Duration scale:** build a scale from instant (color change, opacity) through responsive (hover, focus) to snappy (toggle, small transition) to smooth (modal, panel) to deliberate (page transition, skeleton). Each level should feel distinct. Instant feedback is the fastest the browser can render. Deliberate is slow enough to follow but fast enough to not feel sluggish. Test every transition. Adjust until it feels right, not just until it works.

**Easing:** three curves. Enter: fast start, gentle land. Exit: gentle start, fast disappear. Move: smooth both ends. The enter curve brings content in with energy. The exit curve lets it leave without lingering.

**Orchestration:** one coordinated page load is better than 15 independent animations. Stagger content entry with consistent delay per element, same direction, same easing. If elements animate independently with different timings, it feels broken.

**Reduced motion:** wrap all animation in `@media (prefers-reduced-motion: no-preference)`. When reduced motion is preferred, replace motion with instant opacity transitions, no transform.

## Color Intent

Every color has a job. Document the job, not just the value. A color palette is not a decoration. It is a communication system. The wrong shade of gray for secondary text can make an entire interface feel dead or noisy.

- **Foreground hierarchy:** primary (text), secondary (labels, metadata), muted (placeholders, disabled). Three levels minimum.
- **Surface hierarchy:** base, card/panel, recessed, inset. Four levels.
- **Semantic:** success (green family), warning (amber family), error (red family), info (blue family). These never change meaning.
- **Interactive:** primary action, secondary action, destructive action. Mapped to semantic colors but used for buttons/links specifically.
- **Data:** chart colors that remain distinguishable in all three forms of color blindness (deuteranopia, protanopia, tritanopia). Use 4-6 colors maximum. Avoid red+green adjacency.

**Rule:** if two elements use the same color for different purposes, one of them needs a new token.

## Experience Craft

The difference between a product that works and a product that feels right. This applies to EVERY touchpoint — not just screens. A prompt, a workflow notification, an agent reply, a CLI output, an email, a WhatsApp message — every point where the product touches the user is an experience surface. AI-generated products are functionally correct and experientially dead at every layer, not just the UI.

Experience craft is the WHY behind every behavior pattern. A prompt that leads with the answer exists because the product should feel respectful of the user's time. A loading message that says "Matching recipes from your pantry..." exists because the product should feel like it's working for you, not processing you.

**Feedback principles:**
- Every user action gets acknowledgment. The modality determines the form: visual surfaces use animation (100-200ms). Conversational channels use typing indicators and status messages. CLI uses progress bars. Notifications use delivery confirmation. The principle is universal — the implementation varies.
- Loading and processing states communicate WHAT the system is doing, not just THAT it is doing something. "Checking 3 sources..." not a spinner. "Matching against your pantry..." not "Processing...". This applies equally to a web UI loading state and an agent's first message before delivering a result.
- Success feedback is proportional to achievement. A routine save is subtle. Completing a meaningful goal is a moment. This proportion applies in every modality: a WhatsApp agent's response to "I made the recipe!" should feel warmer than its response to "ok."

**Pacing principles:**
- The product has a rhythm that matches the target feeling. A precise operational tool is fast — answers first, details on demand. A warm reflective product is gentle — context before decision, space to think.
- In conversational flows: answer first, reasoning second. Lead with the insight, offer the "why" on request. Most AI agents dump their chain of thought. That's the agent's experience, not the user's.
- In workflows: communicate progress at each meaningful step, not just at completion. A 3-minute workflow that's silent until the end feels broken. One that says "Step 2/4: Checking availability..." feels alive.
- In prompts: the structure of the prompt shapes the structure of the response. A prompt that asks for a list gets a list. A prompt that asks for a story gets a story. The prompt IS the pacing tool.

**Voice and tone principles:**
- The product speaks with one voice across all surfaces. The same personality in error messages, success messages, agent responses, email subjects, WhatsApp replies, CLI output, and toast notifications. A warm product is warm when it fails too. A precise product doesn't suddenly get chatty in its onboarding email.
- Terminology is consistent across modalities. The dashboard says "3 vehicles need attention." The WhatsApp message says "3 vehicles need attention." Not "3 alerts detected" in one and "attention required for 3 assets" in the other.
- Error communication is actionable everywhere. "Photo too dark to read the receipt. Try with better lighting, or type the items instead" — not "Error: image processing failed." This applies to every error in every channel.

**Gratification principles:**
- Non-functional delight earns its place when it reinforces the target feeling. Visual: a progress ring that fills when the fleet is all-green. Conversational: an agent that remembers you made last week's recipe and asks how it went. CLI: a summary line that shows improvement over last run. Email: a subject line that IS the insight, not a notification label.
- First impressions have personality. The first thing a new user encounters — first screen, first agent message, first CLI output, first email — should embody the product's character.
- Discovery rewards across modalities. A keyboard shortcut hint. An agent that proactively surfaces something useful you didn't ask for. A CLI flag you didn't know about that appears when relevant.

**Restraint principles:**
- For every touchpoint, try removing it. If the feeling survives without it, remove it. This applies to words in a prompt, steps in a workflow, fields in a form, lines in a CLI output, sentences in an agent response.
- Silence is a pattern. Not every event needs a notification. Not every agent response needs an explanation. Not every workflow step needs a status update. The product should be quiet when nothing requires attention.
- In conversation: don't ask "Is there anything else?" after every answer. Don't front-load disclaimers. Don't repeat the question before answering.
- In prompts: every clause in a system prompt that doesn't change the output should be removed. Prompt bloat produces response bloat.

**The feeling test:** After building any touchpoint — a screen, a prompt, an agent flow, a notification template, a CLI output format — ask: does this feel like the same product? Does it carry the target feeling? A "calm control" product should feel calm in its WhatsApp messages as much as in its dashboard. If any touchpoint breaks the feeling, it breaks the product.

## Conversational and Notification Craft

Skip this section if all EIID layers use visual or automated modality.

Non-visual channels deserve the same design attention as visual ones.

**Message structure:** Lead with insight, not context. The recipient knows what happened in the first line, why it matters in the second. Context follows, never leads.

**Information density per channel:**
- SMS: one fact, one action, 160 characters. No formatting available.
- WhatsApp / Slack / Telegram: headline + 2-3 context lines + action. Use native formatting (bold, lists, code blocks where supported).
- Email: full narrative with header, key metrics, recommended action, deep link to visual layer for detail.

**Interaction patterns:** Native to each channel. Reply-based for chat. Button-based where supported (Slack Block Kit, WhatsApp interactive messages). Link-based for email. Forcing web interaction patterns into chat creates friction.

**Formatting as hierarchy:** Bold for headline, plain for context, monospace for IDs and numbers, links for drill-down. The same three-tier hierarchy as visual typography, different tools.

**Timing as design:** When a message arrives is a design decision. Batching low-priority updates into a morning summary is good. A 3am alert for a non-urgent threshold crossing is bad. Delivery cadence communicates priority.

**Cross-channel coherence:** Same numbers, same terms, same framing across dashboard and message. The dashboard shows more detail, but the core statement is identical. A user who reads the Slack message and then opens the dashboard should recognize the same insight immediately.

## Agent Interaction Craft

Skip this section if no EIID layer has a user-facing agent.

When an agent is part of the user experience, how it communicates is a design decision. These patterns apply to any agent that generates output a user sees: chat agents, background processors that report results, automated assistants.

- **Transparency:** show what the agent is doing. "Checking 3 sources..." is better than a spinner. "Scanning your pantry and matching recipes..." is better than "Processing...". Users tolerate latency when they understand the work.
- **Progressive disclosure in output:** answer first, reasoning second. Lead with the result. Offer the chain of thought on request ("Why this recommendation?"), not by default. Most users want the answer, not the process.
- **Clarification patterns:** ask specific questions with sensible defaults. "Thai or Japanese? default: Thai based on your pantry" beats "What cuisine would you like?" Open-ended questions stall the conversation.
- **Handoff agent → UI:** when text is not enough, link to the visual surface. "Full comparison: [link]" or "See the detailed breakdown in your dashboard." The agent knows when a table, chart, or image would serve better than a paragraph.
- **Tool use visibility:** describe actions semantically, not technically. "Checked your pantry" not "get_ingredients(user_123)". "Found 3 matching recipes" not "query_recipes(filter={available: true})". The user sees intent, not implementation.
- **Error communication:** honest and actionable. "Photo too dark to read the receipt. Try with better lighting, or type the items instead." Not "Error: image processing failed" and not "Oops! Something went wrong."

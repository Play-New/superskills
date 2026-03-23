---
description: Design system from EIID mapping through execution. First run determines what needs what surface, defines experience patterns for all modalities, then visual execution for layers that need it. With a target, redesigns it.
allowed-tools: Read, Glob, Grep, Write, Edit, Bash, WebFetch, WebSearch
---

# Design

Read `reference/concepts.md` for shared definitions (experience patterns, absence test, target feeling, rule zero).

## How Design Thinks

Design is not a visual layer. It's the set of decisions that determine how the user perceives the product — across every touchpoint. A WhatsApp message is designed. A CLI output is designed. An agent response is designed. A notification subject line is designed.

Every design decision traces to strategy. The EIID mapping determines what needs attention. The target feeling determines the character. Craft serves strategy. Without that connection, a beautiful interface fails just as hard as an ugly one.

The difference between a product that works and one that feels right: AI-generated products are functionally correct and experientially dead at every layer. Design fixes this by making the experience patterns (see `reference/concepts.md`) concrete and executable.

**Direction spectrum.** Every product sits somewhere on these axes. The point is to lean intentionally:
- **Density:** generous space (editorial, consumer) to packed information (ops dashboard). Task frequency and data volume determine this.
- **Temperature:** warm and organic (grain, rounded, natural colors) to cold and precise (sharp, monospace, high contrast). The user's emotional context determines this.
- **Energy:** calm and restrained (reading, planning) to active and dynamic (monitoring, responding). Task urgency determines this.
- **Complexity:** minimal surface (few elements, deep navigation) to maximal surface (many elements, shallow). The IA determines this.

These axes apply to all modalities: a CLI tool has density and energy. An agent has temperature and complexity.

## Detect Mode

**1. Does a documented design system exist?** Check for `.superskills/design-system.md`, `design-system.md` in root, or `design-system/` directory with direction, tokens, and component docs. Framework config files alone do NOT count — they mean a UI framework is installed, not that design decisions have been made.

**2. Route:**
- **No documented design system** → run **init mode**
- **Documented design system exists + user provided a target** → run **redesign mode**
- **Documented design system exists + no target** → "Design system already exists. Provide a target to redesign. Run `/super:review` for a full design audit."

---

## Init Mode

Two phases. Phase 1 applies to ALL products. Phase 2 applies only to products with visual layers.

If `.superskills/report.md` has a Project Profile, read it for recurring patterns from previous reviews.

### Phase 1: Experience Design (all products)

This phase defines what the product feels like — across every modality. A WhatsApp-only product completes this phase and has a full design system. Visual products complete this phase AND phase 2.

#### Interface Modality Assessment

Read the EIID mapping from CLAUDE.md. For each layer, determine the interface modality by asking:

- **Enrichment:** how does data enter? If users contribute through existing channels (chat, email, photos, voice), the design problem is input format and confirmation, not web forms. If enrichment requires manual configuration (API keys, polling rules), that configuration needs a visual settings surface.
- **Inference:** where do patterns surface? If inference is an internal pipeline, no interface needed. If users configure rules or thresholds, that needs a visual surface. If inference produces real-time status users monitor, that needs a dashboard.
- **Interpretation:** how do users consume insights? Self-contained statements (a number, a verdict, a recommendation) work conversationally. Spatial understanding (charts, maps, multi-variable comparison) requires a visual surface. Most products have both: headline conversational, detail visual.
- **Delivery:** list every output channel. For each: input-only, output-only, or bidirectional? What format constraints?

Produce the EIID Interface Map table (see `reference/design-system-template.md` for format).

**Routing:**
- All layers conversational/notification/embedded → no visual interface needed. Phase 2 skipped. This is a valid, complete outcome.
- Some layers need visual → proceed with phase 2 scoped to those layers.
- All layers need visual → full phase 2.

#### Explore the Product's World

Before choosing any direction, understand the territory. This applies to ALL products — a WhatsApp bot has a world, a voice, a signature.

Read CLAUDE.md for the target feeling. Every design decision tests against it: does this contribute to the feeling, or create noise?

Understand the actual person. Not "users" — where are they? What device, what channel, what moment? A fleet manager getting a WhatsApp alert between site visits is not a developer reading CLI output at their desk.

Understand the verb. Grade submissions. Find the broken deployment. Choose what to cook. The verb shapes the experience.

Translate the target feeling into sensory terms. Not "clean and modern" — that means nothing. Warm like a notebook? Dense like a trading floor? Concise like a colleague? Precise like a surgical tool?

Then produce:
- **Domain concepts:** 5+ metaphors and vocabulary from this product's world. Not features — territory. These apply to naming, voice, terminology across all modalities.
- **Color world:** (visual products only) 5+ colors that exist naturally in this product's domain.
- **Signature:** one element that could only exist for THIS product. Can be visual, behavioral, conversational, or structural.
- **Defaults to reject:** 3 obvious choices you will NOT use. "No generic chatbot greeting" is as valid as "no generic card grid."

For visual products, also consider spatial composition (asymmetry, density variation, grid-breaking), atmosphere (backgrounds as environments), and anti-convergence (if another AI would produce the same output, the design failed).

Do not propose a direction until domain concepts, signature, and defaults to reject are produced.

#### Direction Assessment

Present the exploration findings to the user. Read `.superskills/decisions.md` for research reference seeds from strategy. Then collect input:

**References:** "Any references for how this product should feel? URLs, screenshots, brand names, designers. These can be non-visual: 'the tone of Stripe's error messages', 'the pacing of GitHub Copilot's suggestions.'"

For each reference: fetch/view/research it. Extract concrete patterns — what to borrow (specific and named: "the tight sidebar density from Linear"), what to avoid ("the explanation dumps from ChatGPT"). For design studios or designers: research their philosophy and apply their perspective as a lens, not just their aesthetic.

**Anti-references, existing assets, constraints, direction check.** Collect in one block. The user answers what matters, skips what doesn't.

Process all inputs. Produce a reference summary: 3-7 distilled patterns. Store in `.superskills/design-system.md` under References.

#### Experience Patterns

Read the target feeling from CLAUDE.md. For definitions of each pattern, see `reference/concepts.md`. Translate the target feeling into concrete, observable behaviors for every modality in the EIID Interface Map.

The WHY behind each pattern matters more than the definition:
- A loading message that says "Matching recipes from your pantry..." exists because the product should feel like it's working FOR you, not processing you.
- An agent that leads with the answer exists because the product should feel respectful of time.
- A WhatsApp agent's response to "I made the recipe!" should feel warmer than its response to "ok" — because gratification is proportional.
- Silence when nothing requires attention exists because quiet IS the experience.
- Prompt bloat produces response bloat — every clause that doesn't change the output should be removed.

Define per-modality behavior. Not every product has every modality — define only for modalities present in the EIID Interface Map.

**Coherence test:** after defining patterns, ask: does a user interacting via dashboard, then WhatsApp, then email feel like they're using the same product? Same voice, same terminology, same personality.

Write to `.superskills/design-system.md` under Experience Patterns.

#### Interaction Patterns for Non-Visual Layers

For EIID layers mapped to conversational, notification, or embedded modality:

**Message craft.** Lead with insight, not context. The recipient knows what happened in the first line, why it matters in the second. Information density adapts per channel: SMS is one fact in 160 chars; WhatsApp is headline + 2-3 lines + action with native formatting; email is full narrative with deep link. Formatting IS hierarchy: bold for headline, plain for context, monospace for data. Timing is a design decision — batching low-priority updates into a morning summary is good, a 3am alert for non-urgent threshold crossing is bad. Same numbers, same terms, same framing across dashboard and message.

**Agent interaction craft.** (Skip if no user-facing agent.) How the agent communicates is a design decision:
- **Transparency:** show what it's doing. "Checking 3 sources..." Users tolerate latency when they understand the work.
- **Progressive disclosure:** answer first, reasoning second. Most agents dump their chain of thought. That's the agent's experience, not the user's.
- **Clarification:** specific questions with sensible defaults. "Thai or Japanese? default: Thai based on your pantry" beats "What cuisine would you like?"
- **Handoff:** when text isn't enough, link to the visual surface. The agent knows when a table or chart serves better.
- **Tool visibility:** "Checked your pantry" not "get_ingredients(user_123)".
- **Errors:** honest and actionable. "Photo too dark. Try better lighting, or type the items instead."

Non-visual products complete their design system here, then write it (see Write Design Configuration below).

---

### Phase 2: Visual Execution (visual layers only)

The problem-solving is done. Phase 1 defined what the product needs, how it should feel, and how it speaks. This phase defines how it looks.

#### Information Architecture

The visual surface handles what channels cannot: visualizations (charts, maps, timelines) and configuration of the invisible layer.

Priority from the EIID mapping: interpretation visualizations get the most screen space. Inference outputs support them (alerts, scores). Enrichment configuration lives deep (set once). Delivery configuration is a settings concern.

Define: core objects (3-6 nouns of the product), navigation budget (fewer is better — settings and integrations go inside other items), screen map (one focal point per screen, above-the-fold content, content depth tiers: surface / one click / deep).

Do not proceed to visual styling until IA is confirmed.

#### Style Direction

The product's character becomes visual shape. Three dimensions:
- **Density:** tight (data-heavy, admin) vs. generous (consumer, editorial). Task frequency determines this.
- **Shape:** sharp/boxy (technical) vs. rounded/pill (approachable). Product temperature determines this.
- **Weight:** compact (efficiency-first) vs. spacious (comfort-first). Time pressure determines this.

For projects with a component library and style presets, pick the matching preset with reasoning. For projects without, encode these decisions directly in the theme.

#### Typography

Brand fonts first. If none: choose based on the product's character, not popularity. **The default test:** if the font ships with the framework, it's not a design decision. Browse foundries with the product's character in mind.

How many type voices does this product need? One font at different weights, a display + body pair, or three (display, body, mono). The number of typefaces is a design decision.

Full type scale: Display through Mono, each with size, weight, line height, tracking, usage. **Hierarchy test:** strip color. If hierarchy collapses, add more size or weight contrast. Tighter line heights for headings, generous for body. Monospace for data needing alignment.

#### Layout

Spatial structure, decisions flowing from IA:
- **Grid:** column count, gutter size. Not every product needs 12 columns.
- **Breakpoints:** pixel values and what shifts at each.
- **Container:** max-width strategy for different screen types.
- **Page patterns:** 3-5 recurring structures mapped to screen types from the IA.

#### Composition

Five decisions between layout and tokens:
- **Hierarchy:** how primary/secondary/tertiary are distinguished. Must work without color.
- **Density map:** which zones are dense, which generous, and why.
- **Section rhythm:** three numbers (major sections / groups / items).
- **Proportion:** focal point vs supporting content size.
- **Whitespace:** page margins, breathing room, grouping vs separating gaps.

**Spatial composition:** density variation creates rhythm (dense = scan, open = focus). Intentional asymmetry creates hierarchy. One grid-breaking element per screen creates tension. Negative space communicates: large margins = important, tight margins = related.

#### Tokens

All aesthetic choices become design tokens. No creative decision lives in component code.

**Extract before propose:** scan existing code for repeated values. Count occurrences, formalize the most common ones.

Every UI framework manages tokens differently — use the native approach. The principles are constant: tokens define the aesthetic (colors, spacing, typography, radius, depth), token names evoke the product's world, dark mode tokens exist alongside light, install only components the IA requires.

**Visual craft in the tokens:**
- **Surfaces** layer like paper — lightness shifts between adjacent levels, felt not counted. Border opacity uses foreground color at low opacity. Shadows share angle and hue, scale by elevation. Dark mode: surfaces get lighter as they elevate.
- **Color intent:** every color has a job. Foreground hierarchy (3 levels), surface hierarchy (4 levels), semantic (never change meaning), interactive (primary/secondary/destructive), data (4-6 chart colors, colorblind-safe). If two elements share a color for different purposes, one needs a new token.
- **Motion:** communicates state change, not decoration. Duration scale (instant to deliberate), consistent easing, coordinated orchestration, reduced motion respected.
- **Atmosphere:** backgrounds as environments, not fill colors. Optional — absence is also a choice.

**Key rule:** the token file IS the aesthetic. Strip all components and rebuild from tokens alone — the product should still look the same.

---

### Write Design Configuration

Follow `reference/design-system-template.md` for output structure.

Write to two places:

**CLAUDE.md** — Design System section (framework, style, token source, direction, navigation, typography, color character, signature).

**`.superskills/design-system.md`** — full design decisions: EIID Interface Map, direction, experience patterns, references, information architecture, layout, typography scale, composition, tokens, component patterns, conversational patterns, agent interaction patterns, decisions log.

---

## Redesign Mode

Craft improvement on a single target, grounded in strategy. Consistency audits belong in review mode.

### 1. Load Context

Read the EIID Interface Map from `.superskills/design-system.md` (or extract from code). If the redesign target belongs to a non-visual layer, focus on message structure and channel design.

Read whatever design context exists. If `.superskills/design-system.md` exists, read it for direction, patterns, tokens, component patterns. If not, extract from code: scan theme files and 5-10 components for repeated values to build a working picture of the current design system.

Read CLAUDE.md for EIID mapping, user context, and target feeling. Read `reference/design-critique.md` for the 6-layer critique framework.

### 2. Identify Target

Read the target files. View screenshots or images. Fetch URLs. Establish the current state. If the user pointed at a screenshot without file paths, ask which source files implement it.

### 3. Intent Assessment

Show the user what you see. Then collect: what's wrong, what works (keep as-is), references for this screen specifically, and priority (experience, usability, density, craft, or structure). If a target feeling exists, ask if the screen matches it. Skip questions the user already answered.

For each reference: fetch, view, research. Extract applicable patterns.

### 4. Critique

Apply the 6-layer critique from `reference/design-critique.md`. Test against the target feeling and experience patterns first — strategic alignment overrides all other layers. Score each layer: strong, adequate, weak. Prioritize based on the user's stated priority.

### 5. Propose

For each proposed change:

| Layer | Current | Proposed | Why |
|-------|---------|----------|-----|
| [layer] | [specific] | [specific] | [serves target feeling + craft principle + design system constraint] |

Present the full proposal. Wait for confirmation.

### 6. Implement

Apply changes through the design system, not around it. Semantic tokens, not raw values. Search for existing components before building custom. Follow the framework's conventions.

Update `.superskills/design-system.md` if new patterns were established. Log to `.superskills/decisions.md`.

### Redesign Rules

- One target at a time. No scope creep.
- Propose before implementing.
- Use existing tokens. Flag new ones.
- Design system gaps get fixed in the system, not locally.
- Keep what works.
- Every change traces to feeling, strategy, or craft. "This looks better" is not a rationale.

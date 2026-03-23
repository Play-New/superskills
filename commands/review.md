---
description: Full quality audit. Tests, security, build standards, strategy, experience, design, performance, agent architecture. All at once.
allowed-tools: Read, Glob, Grep, Write, Edit, Bash
---

# Review

Full audit of the project. Run tests first (broken code makes other audits unreliable), then the remaining seven domains sequentially.

**Review reports. Review does not fix.** Every finding is a report entry with location, severity, and suggested fix. The user decides what to fix and when. Do not modify source code, configuration, dependencies, or infrastructure during review. The only files review writes to are `.superskills/report.md` and `.superskills/decisions.md`.

## Prerequisites

Read CLAUDE.md. If no EIID mapping exists, stop and suggest `/super:strategy` first.

---

## 1. Tests

### Detect Mode

Check for vitest.config.ts OR a `test` script in package.json. If found, run **verify**. Otherwise, run **init**.

### Init

1. **Install test runner and E2E framework.** Detect if the project already uses a test runner (jest, vitest, mocha). If not, install vitest as dev dependency. Install @playwright/test for E2E and install Playwright browsers.

2. **Configure test runner.** Create or update the config file. Set up test directory structure: `tests/unit/`, `tests/e2e/`.

3. **Add test scripts to package.json.** Three scripts: run tests once, run in watch mode, run E2E tests.

4. **Configure Playwright.** Create the config file with local dev server and base URL.

5. **Stack-adaptive setup.** Detect the project's stack from package.json. For each significant dependency, identify its test utilities and mocking patterns. Database clients need mocked clients for unit tests. Workflow engines often provide test harnesses. Frameworks need request mocking for API route tests.

6. **Write smoke tests:** unit smoke test (core utilities work), E2E smoke test (home page loads and renders).

7. **Run tests to verify:** `npm test`

### Verify

Run in sequence:

1. `npm test` (add `-- --run` for vitest, `-- --watchAll=false` for jest)
2. If Playwright configured: `npx playwright test`
3. `npx tsc --noEmit`

### Gate

**Block while tests fail.** Do not proceed to remaining audits automatically. Ask whether to continue with the remaining audits or stop to fix failures first.

---

## 2. Security

Severity: blocking on critical findings, HIGH/MEDIUM/LOW otherwise. Report all findings — do not apply fixes.

Read `reference/review-security-guide.md` for the full security audit checklist. Apply all checks relevant to the detected stack. Block on: credentials in code, SQL/NoSQL injection, XSS, authentication bypass, PII exposure without consent.

For each finding, report: severity, file:line, what's wrong, and how to fix it. The user or `/super:build` applies the fix.

---

## 3. Build Quality

Severity: blocking on security posture, advisory on design.

Read `reference/build-principles.md`. Apply to the existing codebase:

### Minimal Surface
- Are there screens, endpoints, tables, or components that don't trace to the EIID mapping? Flag each.
- Are there screens doing two things that should be one? Settings split across multiple pages? Redundant views?
- Are there tables that aren't queried, endpoints that aren't called, components that aren't rendered?
- Count: how many screens, tables, API routes. Compare against the EIID mapping. If the ratio feels heavy, flag it.

### Design Through the Stack
- Does the schema reflect the IA's core objects, or is it over-modeled?
- Do visual surfaces carry the design direction's character, or do they feel generic?
- Is the signature element present and visible?
- Do user-facing messages (errors, notifications, agent responses) match the design direction's tone?

### Security Posture
- If the product has users: single auth middleware, not scattered checks. Blocking.
- If the product has a database with user data: RLS enabled. Blocking.
- Secrets in env, validated at startup, never in code. Blocking.
- Input validation at boundaries (user input, webhooks, API responses). Blocking on critical paths.

### Intelligence Transparency (if LLM/agent components exist)
- Are prompts in discoverable locations, not inline in business logic?
- Do LLM calls have schema-validated output?
- If user-facing: can the user see what the system is prompted to do?
- Are graduation markers present in code comments?

---

## 4. Strategy

Read CLAUDE.md EIID mapping. For each source file:
1. Which EIID layer does it support? (enrichment / inference / interpretation / delivery / none)
2. If "none": is it supporting infrastructure (tests, types, config, build, utilities used by EIID layers)? Expected, not scope creep. If it's application code that doesn't serve any EIID layer, flag as potential scope creep.
3. Any dependency not traceable to the EIID mapping? Flag it.

Run the opportunity scan:

**Enrichment:**
1. Any data source connected but not used for inference?
2. Any two data sources that could be cross-referenced but are not?
3. Any public API or external data that would fill a gap in the current schema?

**Inference:**
4. Any collected data with patterns not being analyzed?
5. Any prediction the existing data supports but nobody built?
6. Any anomaly detection that would catch problems before users notice?

**Interpretation:**
7. Any analysis result generated but not surfaced to users?
8. Any insight that lacks context (trend, comparison, explanation) to be actionable?

**Delivery:**
9. Any channel the users frequent that the system does not reach?
10. Any timing improvement (deliver sooner, deliver at the right moment)?
11. Any trigger condition that would catch events currently missed?

---

## 5. Experience

Severity: advisory, but high-impact findings can block.

Read CLAUDE.md for the target feeling. Read `.superskills/design-system.md` for experience patterns. If neither exists, skip.

### Experience Alignment

For each touchpoint the user perceives — screens, agent responses, notifications, prompts, CLI output, workflow messages, emails:
1. **First impression test:** what sensation does this touchpoint produce? Does it match the target feeling?
2. **Absence audit:** for each element the user perceives (visual or textual), would the target feeling survive without it? Flag candidates for removal.
3. **Noise audit:** is there clutter that undermines the feeling? Visual: competing animations, toast storms. Conversational: explanation dumps, unnecessary follow-up questions. Prompts: clauses that don't change output. Workflows: notifications for routine steps.

### Experience Pattern Compliance

If `.superskills/design-system.md` has an Experience Patterns section:
1. **Feedback:** does every user action get acknowledgment appropriate to its modality? Visual: animation/state change. Conversational: status message. CLI: progress indicator. Workflow: step update.
2. **Pacing:** does the rhythm match the target feeling? Fast and precise, or gentle and spacious? Check across all modalities — an agent that dumps a wall of text breaks pacing even if the dashboard is clean.
3. **Voice consistency:** does the product speak with one voice? Same tone, same terminology across dashboard, agent responses, error messages, notifications, emails, CLI output. Flag divergences.
4. **Gratification:** are meaningful completions marked with proportional feedback in each modality? Is mundane interaction appropriately quiet?
5. **Restraint:** are there touchpoints that could be removed or simplified? Confirmation dialogs where undo works. Agent explanations nobody asked for. Workflow notifications for routine completions. Prompt clauses that add no value.

### Cross-Modality Coherence

1. **Same product test:** if a user interacts via dashboard, then via WhatsApp, then reads an email — do all three feel like the same product?
2. **Terminology match:** same terms, same framing, same units across all surfaces.
3. **If visual surfaces exist:** touch targets 44x44px minimum, experience survives across breakpoints, hover patterns have touch equivalents.

---

## 6. Design

Read CLAUDE.md and `.superskills/design-system.md` for context. Read `reference/design-critique.md` for the full critique framework. Work through all six layers (0 through 5).

### EIID Modality Alignment

Severity: blocking if widespread, warning if isolated.

Read `.superskills/design-system.md` for the EIID Interface Map. If no EIID Interface Map section exists, skip this subsection.

1. **Visual scope match:** Are there screens or pages for EIID layers mapped to non-visual modalities? Each is a candidate for removal or conversion to a conversational pattern.
2. **Missing visual surfaces:** Are visual-mapped layers missing screens or components? Each gap means the product can't deliver on that layer's promise.
3. **Channel coverage:** Are conversational or notification layers implemented? Missing channels mean delivery failures.
4. **Cross-channel consistency:** Do the same terms, metrics, and framing appear in both visual and conversational surfaces? Inconsistency confuses users who use multiple channels.

### Accessibility (blocking)

WCAG 2.1 AA:
1. Color contrast: 4.5:1 normal text, 3:1 large text
2. Focus states on all interactive elements
3. Alt text on all images
4. Labels on all form inputs (not just placeholder)
5. Touch targets: 44x44px minimum
6. Semantic HTML: proper heading hierarchy, landmarks, ARIA
7. Keyboard navigation: all interactive elements reachable via tab
8. `cursor-pointer` on all clickable elements

### Information Architecture (blocking if widespread)

Read `.superskills/design-system.md` for the IA section. If no IA is documented, skip.

1. **Navigation budget:** compare actual nav items against documented budget
2. **Focal point:** compare actual visual weight against documented focal points
3. **Content depth:** check tier violations (enrichment config on surface, delivery outputs buried deep)
4. **Screen coverage:** compare implemented screens against documented screen map

### Design System Compliance (blocking if widespread)

If `.superskills/design-system.md` has Layout, Typography Scale, and Composition sections, check compliance:
1. **Layout:** components use documented grid, breakpoints, container strategy, and page patterns. Flag undocumented breakpoints or layouts that bypass the grid.
2. **Typography:** text elements use levels from the documented scale. Flag sizes or weights not in the scale.
3. **Composition:** density zones match the documented density map. Section spacing matches the documented rhythm. Flag uniform spacing where the map says it should vary.

If no design system with these sections exists, skip.

### Conversational and Agent Pattern Compliance (blocking if widespread)

If `.superskills/design-system.md` has Conversational Patterns or Agent Interaction Patterns sections:
1. **Message structure:** do implemented messages match documented patterns? Lead with insight, correct information density per channel, native interaction patterns.
2. **Agent interaction:** do agent responses follow documented transparency, clarification, handoff, and error patterns? Flag agents that show raw tool calls, ask open-ended questions without defaults, or return technical errors to users.
3. **Cross-channel terms:** do message templates and visual surfaces use the same terminology, units, and framing?

If no conversational or agent patterns are documented, skip.

### Cross-File Consistency (blocking if widespread)

Compare across all component files:
1. **Type scale:** same heading sizes, body text sizes, and weights everywhere
2. **Spacing rhythm:** consistent spacing multiples across pages
3. **Surface treatment:** same card styles, same border usage, same shadow levels
4. **Color usage:** same semantic colors for same purposes

### Framework Rules

- **shadcn + Tailwind:** semantic tokens only (no raw hex/rgb), `gap-*` for containers, `data-slot` attributes, CVA for variants, spacing matches style preset, search registries before building custom
- **Chakra/MUI/Mantine:** all styling through framework APIs, theme overrides in theme file
- **Tailwind only:** utility classes only, no arbitrary values

### Signature Element (advisory)

If `.superskills/design-system.md` documents a signature element, verify it is present in the built product. The signature is the one thing that makes this product recognizable — if it's documented but missing from the implementation, the product feels generic. Check: does the signature appear where users encounter the product most frequently?

### Craft (advisory)

Apply all six critique layers from `reference/design-critique.md`, evaluating craft dimensions from `reference/design-craft.md`. Include conversational and notification craft for non-visual layers. Include agent interaction craft for user-facing agents. Flag craft issues as suggestions, not violations.

---

## 7. Performance

Severity: blocking on regressions beyond budget, advisory otherwise.

**Detect mode.** Check if `.superskills/report.md` has a Performance Budget section. If not, run the init steps from `reference/review-performance-guide.md` to establish baseline targets and tooling first. If it exists, run review mode and measure against those targets.

Read `reference/review-performance-guide.md` for the full performance audit checklist. Block on: bundle size regression >20%, missing lazy loading for routes, unindexed queries on user-facing paths.

---

## 8. Agent Architecture

Skip this section entirely if no EIID component uses agent, workflow, or LLM call.

Severity: blocking on parity gaps in critical paths, advisory otherwise.

### Parity Audit

For each user action available in the UI, check: is there a corresponding tool an agent can call? List the gaps. Missing parity means agents cannot do what users can. Critical-path gaps (actions users perform daily) are blocking.

### Implementation Level Check

For each component classified via LLM call, workflow, or agent in the EIID mapping:
- Does the implementation match the classified level?
- An LLM call that uses tool loops or retries is under-classified (should be workflow or agent).
- An agent that follows a fixed path every time is over-classified (should be workflow).
- A workflow with a single step is over-classified (should be LLM call).

### Graduation Readiness

For each component with a graduation trigger documented in the Approach field:
- Is volume approaching the threshold? Check logs or usage metrics.
- Have patterns stabilized? If the last 20 invocations follow the same path, the agent is acting like code.
- Are edge cases still the driver of value? If yes, graduation is premature.

### Tool Quality

For each tool exposed to agents:
- **Atomicity:** does the tool do one thing? "process_order" should be split into primitives.
- **Schema validation:** does the tool validate inputs and outputs against a schema?
- **Descriptions:** are tool descriptions accurate enough for an agent to choose correctly?
- **Error structure:** do errors include what went wrong and what to try next?

### Agent Testing Strategy

- Tests define the outcome, agent iterates until pass. Are tests written this way?
- Integration test of the full agent loop (not just individual tools).
- Cost tracking: is token usage logged per invocation? Can monthly cost be computed?

### Feedback Loop

- Are agent failures logged with enough context to diagnose?
- Do captured failure patterns inform graduation decisions?
- Is there a mechanism to review agent outputs periodically?

---

## Output

Write all findings to `.superskills/`:

- Test results → **replace** Test Report section in `.superskills/report.md` (keep last 3 runs)
- Security findings → **replace** Security Findings section in `.superskills/report.md`
- Build quality findings → **replace** Build Quality section in `.superskills/report.md`
- Strategy findings → **append** to `.superskills/decisions.md`
- Experience findings → **replace** Experience section in `.superskills/report.md`
- Design findings → **replace** Design Findings section in `.superskills/report.md`
- Performance findings → **replace** Performance Budget section in `.superskills/report.md`
- Agent architecture findings → **replace** Agent Architecture section in `.superskills/report.md`

**Update status counts** at the top of `.superskills/report.md`.

**Update Project Profile** in `.superskills/report.md`: note which EIID layers have the most code, which issues recur, and any patterns learned.

Print a summary at the end:

```
## Review Summary — [date]

Tests:        [passed/failed/skipped]
Security:     [blocking count] blocking, [high count] high
Build:        [surface count] excess, [security count] security issues
Strategy:     [scope-creep count] scope creep, [opportunity count] opportunities
Experience:   [alignment issues] alignment, [pattern issues] patterns, [coherence issues] coherence
Design:       [violation count] violations
Performance:  [issue count] issues
Agent:        [issue count] issues (or "skipped — no agent components")

Blocking:     [yes/no]
```

Security output table format:

| Severity | File:line | Issue | Fix |
|----------|-----------|-------|-----|
| BLOCK/HIGH/MEDIUM/LOW | path:line | description | what to do |

Performance output table format:

| Category | Issue | Impact | Fix | Estimated Savings |
|----------|-------|--------|-----|-------------------|
| bundle/cwv/database/api-costs/caching | description | quantified | what to change | time or money saved |

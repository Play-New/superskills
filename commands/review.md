---
description: Full quality audit. Tests, security, build standards, strategy, experience, design, performance, agent architecture. All at once.
allowed-tools: Read, Glob, Grep, Write, Edit, Bash
---

# Review

Full audit of the project. Run tests first (broken code makes other audits unreliable), then the remaining seven domains sequentially.

**Review reports. Review does not fix.** Every finding is a report entry with location, severity, and suggested fix. The user decides what to fix and when. Do not modify source code, configuration, dependencies, or infrastructure during review. The only files review writes to are `.superskills/report.md` and `.superskills/decisions.md`.

## Prerequisites

Read CLAUDE.md. If no EIID mapping exists, stop and suggest `/super:strategy` first.

Read `.superskills/report.md` Project Profile section if it exists. Note recurring patterns, previously flagged issues, and which EIID layers have the most findings. Use this context to prioritize: recurring issues that survived a previous review deserve higher severity.

For shared definitions (EIID, implementation levels, graduation, target feeling, experience patterns, absence test, Rule Zero), see `reference/concepts.md`.

---

## 1. Tests

### Detect Mode

Check for vitest.config.ts OR a `test` script in package.json. If found, run **verify**. Otherwise, run **init**.

### Init

Read the EIID mapping and the stack from CLAUDE.md. The product's shape determines what testing infrastructure it needs.

**Derive what to install.** Not every product needs the same tools:
- Products with visual surfaces need a browser-based test runner (Playwright or equivalent) to verify what the user sees.
- Products with API routes, business logic, or agent behavior need a unit/integration runner (vitest, jest, or whatever the project already uses).
- CLI products need a way to test command output and exit codes.
- Conversational products need a way to test message structure and agent responses.

Install only what this product requires. Detect existing test infrastructure first — don't replace what works.

**Configure and verify.** Set up the runner, add scripts to package.json, write one smoke test per test type to confirm the infrastructure works. Run them.

**Stack-adaptive setup.** For each significant dependency, identify its test utilities and mocking patterns. Database clients need mocked clients for unit tests. Workflow engines often provide test harnesses. Frameworks need request mocking for API route tests.

### Verify

Run all configured test suites in sequence: unit/integration tests, then browser tests if configured, then type checking. Use whatever commands the project has set up — read package.json scripts, detect the test runner, and run accordingly.

### Gate

**Block while tests fail.** Do not proceed to remaining audits automatically. Ask whether to continue with the remaining audits or stop to fix failures first.

---

## 2. Security

Severity: blocking on critical findings, HIGH/MEDIUM/LOW otherwise. Report all findings — do not apply fixes.

### OWASP Top 10

Scan all source files. For each item: PASS or FAIL with file:line.

1. **Broken access control.** API routes without auth checks? Missing RLS? Server actions callable without session validation?
2. **Injection.** Unsanitized user input in queries, commands, templates? String concatenation in SQL/NoSQL instead of parameterized queries?
3. **XSS.** Unescaped user content rendered as HTML? `dangerouslySetInnerHTML` or equivalent without sanitization?
4. **SSRF.** User-provided URLs fetched server-side without validation? Missing allowlist for external requests?
5. **Security misconfiguration.** Debug mode on? CORS too permissive? Missing security headers (CSP, X-Frame-Options, X-Content-Type-Options)?
6. **Vulnerable components.** Run `npm audit`. Flag critical/high. Check for outdated dependencies with known CVEs.
7. **Auth failures.** Weak session handling? Missing rate limiting on login/signup? No account lockout after failed attempts?
8. **Data integrity.** Missing input validation on Server Actions or API routes? No schema validation (zod, yup, etc.) on user-facing endpoints?
9. **Logging failures.** Security events not logged? PII in logs? No audit trail for admin actions?
10. **CSRF.** State-changing actions without CSRF tokens? Missing SameSite cookies? GET requests that mutate state?

### Secrets Scan

- Hardcoded API keys, passwords, tokens in source files
- `.env` or `.env.local` in .gitignore?
- Secrets in git history (search for common patterns: `sk-`, `AKIA`, `ghp_`, `password =`, `secret =`)
- Service account JSON files committed to the repo
- Private keys or certificates in the source tree

### GDPR Compliance

**Applicability gate:** Does this product collect or process personal data? If no user accounts, no PII in any data flow, and no EU users, skip entirely. If applicable: consent before collection, data export/deletion capabilities, data minimization, retention policy, breach notification process.

### Stack-Adaptive Security Checks

Detect from package.json. For tools not listed, identify their security surface and apply equivalent checks.

- **Database with auth:** RLS on all user-data tables? Admin keys server-side only? Auth middleware on protected routes? Storage policies? Realtime respects RLS?
- **Hosting:** env vars split preview/production? Security headers? No secrets in deployment config? Preview deployments restricted?
- **Workflow engine:** webhook signing keys validated? No PII in event payloads? Steps idempotent? Errors don't leak internal state?
- **Framework:** server actions validate input? API routes check auth? Route params sanitized? Middleware on all protected groups?
- **Payment:** webhook signature verification? Amount validated server-side? Subscription status checked before access?

### Agent Security

Skip if no agent components.

- **Isolation:** OS-level sandboxing (containers, not application access controls). Each agent in its own sandbox with selective data mounting — only what the job needs.
- **No secrets in agent environment.** Keys stay host-side. Agents access tools through controlled interfaces.
- **Treat agents as untrusted.** Design the sandbox assuming the agent will use every capability it has.
- **Agent-to-agent isolation.** Multiple agents must not share environment, filesystem, or database access.
- **Audit trail.** Log file writes, network calls, tool invocations, and data access.

### Adversarial Testing for LLM Components

Skip if no user input reaches an LLM.

- **Prompt injection.** Test direct ("ignore previous instructions"), indirect (instructions in uploaded docs/images), and encoding tricks (base64, unicode). Every user-facing prompt needs a system boundary the user cannot override.
- **Output manipulation.** Can the LLM output harmful content or fake system messages? Schema validation catches structural, content filtering catches semantic.
- **Tool abuse.** Can a user message trick the agent into unauthorized tool calls? Each tool must enforce its own authorization independent of agent reasoning.
- **Data exfiltration.** Can the agent be convinced to leak other users' data, system prompts, or keys? Context should never contain data unauthorized for the current user.
- **Memory poisoning.** If cross-session memory exists: can a user plant instructions that affect future sessions? Memory stores facts, not instructions.

### Blocking Rules

**BLOCK** (must fix before proceeding): credentials in source, SQL/NoSQL injection, XSS, auth bypass on protected routes, PII exposure in logs/errors/URLs. Everything else: HIGH / MEDIUM / LOW.

For each finding, report: severity, file:line, what's wrong, and how to fix it. The user or `/super:build` applies the fix.

---

## 3. Build Quality

Severity: blocking on security posture, advisory on design.

Apply rule zero (see `reference/concepts.md`) to the existing codebase:

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

Run the opportunity scan per EIID layer:

- **Enrichment:** data sources connected but unused for inference? Sources that could be cross-referenced? External data that fills a schema gap?
- **Inference:** collected data with unanalyzed patterns? Predictions the data supports but nobody built? Anomaly detection that would catch problems early?
- **Interpretation:** analysis results generated but not surfaced? Insights lacking context (trend, comparison, explanation) to be actionable?
- **Delivery:** channels users frequent but the system doesn't reach? Timing improvements? Trigger conditions that would catch missed events?

---

## 5. Experience

Severity: advisory, but high-impact findings can block.

Read CLAUDE.md for the target feeling. Read `.superskills/design-system.md` for experience patterns. If neither exists, skip. For definitions of target feeling, experience patterns, and absence test, see `reference/concepts.md`.

### Experience Alignment

For each touchpoint the user perceives — screens, agent responses, notifications, prompts, CLI output, workflow messages, emails:
1. **First impression test:** what sensation does this touchpoint produce? Does it match the target feeling?
2. **Absence audit:** for each element the user perceives, would the target feeling survive without it? Flag candidates for removal.
3. **Noise audit:** is there clutter that undermines the feeling? Visual: competing animations, toast storms. Conversational: explanation dumps, unnecessary follow-up questions. Prompts: clauses that don't change output. Workflows: notifications for routine steps.

### Experience Pattern Compliance

If `.superskills/design-system.md` has an Experience Patterns section, check each pattern (feedback, pacing, voice, gratification, restraint) against implementation. See `reference/concepts.md` for the canonical definition of each pattern.

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

### Framework Conventions

Detect the UI framework from package.json. Verify that the project follows the framework's conventions for token management, component APIs, and styling. The principle: all styling flows through the design system — semantic tokens, not raw values. Framework APIs and theme overrides, not inline escapes. Search for existing components before building custom ones. Flag deviations.

### Signature Element (advisory)

If `.superskills/design-system.md` documents a signature element, verify it is present in the built product. The signature is the one thing that makes this product recognizable — if it's documented but missing from the implementation, the product feels generic. Check: does the signature appear where users encounter the product most frequently?

### Craft (advisory)

Apply all six critique layers from `reference/design-critique.md`. Include conversational and notification craft for non-visual layers. Include agent interaction craft for user-facing agents. Flag craft issues as suggestions, not violations.

---

## 7. Performance

Severity: blocking on regressions beyond budget, advisory otherwise.

### Detect Mode

Check if `.superskills/report.md` has a Performance Budget section. If not, run **init**. If it exists, run **review**.

### Init

Derive targets from the product's context. Not every product has the same performance needs:
- A visual SaaS used daily needs tight Core Web Vitals because users notice lag on pages they visit hundreds of times. Use Google's thresholds as starting points: LCP < 2.5s, CLS < 0.1, INP < 200ms. Set bundle and API response targets based on the product's complexity.
- A CLI tool cares about execution time and output speed, not web vitals.
- A conversational product cares about response latency — how fast the agent or workflow returns an answer.
- A background workflow cares about throughput and cost, not user-facing latency.

For non-visual products, define equivalent targets: CLI execution time, agent response time, workflow throughput. Configure the appropriate measurement tools for the stack. Write the budget to `.superskills/report.md` Performance Budget section.

### Review

Measure against established targets.

**Bundle analysis.** Run the framework build command and check output sizes. Identify largest dependencies (anything >50KB gzipped deserves scrutiny). Check for: missing dynamic imports for heavy libraries (chart libs, editors, syntax highlighters), barrel file imports that defeat tree-shaking, duplicate dependencies (two versions of the same library), unused CSS or duplicate utility classes.

**Core Web Vitals** (visual surfaces only):
- **LCP:** hero images, large text blocks, above-fold components. Images without framework optimization? Fonts without framework font loading? Render-blocking resources? Slow TTFB?
- **INP:** heavy synchronous operations during interaction? Long tasks (>50ms) on user input? State updates causing large re-renders? Third-party scripts blocking main thread?
- **CLS:** images without explicit dimensions? Dynamic content injected above the fold after initial render? Font swap causing shift? Embeds without reserved space?

**Database performance.** N+1 query patterns. Missing indexes on WHERE/ORDER BY/JOIN columns. Over-fetching (SELECT * instead of specific columns). Under-batching (single inserts in loops instead of batch). Unindexed queries on user-facing paths (blocking). Large result sets without pagination.

**API cost analysis.** Count LLM call sites. Estimate tokens per call (input + output) and $/month at projected volume. Caching opportunities for repeated identical inputs. Model selection: expensive models where cheaper ones suffice? Streaming responses where appropriate?

### Stack-Adaptive Performance Checks

Detect from package.json. Apply for each detected tool. For tools not listed, identify their performance surface and apply equivalent checks.

- **Database (Supabase, Prisma, Drizzle, etc.):** select specific columns, not all. Batch operations instead of insert loops. Indexes on filtered/sorted columns. Connection pooling for serverless.
- **Hosting (Vercel, Netlify, etc.):** static/incremental regeneration for semi-static pages. Edge runtime for latency-sensitive routes. Image and font optimization via platform tools. CDN caching headers.
- **Workflow engine (Inngest, Trigger.dev, etc.):** sleep/wait instead of polling loops. Batching for high-frequency events. Concurrency limits. Fan-out instead of sequential processing.
- **Framework (Next.js, Remix, Nuxt, etc.):** dynamic imports for heavy client components. Server-first rendering. Streaming for slow data sources. Route-level static/dynamic configuration. Prefetching for likely navigation. Suspense boundaries around slow components.

**Block on:** bundle size regression >20% from budget, missing lazy loading for routes, unindexed queries on user-facing paths.

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

Write findings to `.superskills/`. Each domain **replaces** its section in `.superskills/report.md` (test results keep last 3 runs). Strategy findings **append** to `.superskills/decisions.md`.

**Update status counts** at the top of `.superskills/report.md`. **Update Project Profile:** note which EIID layers have the most code, which issues recur, and patterns learned.

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

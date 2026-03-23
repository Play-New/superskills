# Build Principles

Not a checklist. Not a boilerplate. These are design principles for construction — how to decide what to build, how much to build, and how to know when to stop.

Read CLAUDE.md and `.superskills/design-system.md` before applying. Every decision below is filtered through the strategy and the design direction.

---

## 1. Minimal Surface

The best products do fewer things, better. Every screen, endpoint, table, component, setting, and dependency is a cost: cognitive load for the user, maintenance load for the developer, context load for the AI.

**Before adding anything, ask:**
- What user need does this serve? (trace to EIID)
- What's the simplest version that delivers that value?
- What happens if I don't build this? If the answer is "nothing changes for the user", don't build it.

**Common traps:**
- Settings page with 20 options when 3 matter. Let the system infer the rest.
- Dashboard with 8 cards when 2 carry 90% of the insight. Let the focal point breathe.
- Schema with tables for every noun in the domain. Build tables for what the product actually stores and queries.
- API routes for operations nobody calls. Build endpoints for real interactions.

Minimal surface is not "build an MVP." It's "build the complete product with nothing unnecessary." The product should feel finished AND simple.

## 2. Design Runs Through Everything

Design is not a visual layer applied after construction. It's the structure of the product. How the information architecture shapes the schema. How the density direction shapes the API (what data to return, how much, in what groups). How the signature element influences every screen and every message.

**Schema follows IA.** The core objects in the information architecture (3-6 from the design system) are the core tables. If the IA says "Fleet, Vehicle, Alert, Route" then those are the tables. Not "users, organizations, fleet_assignments, vehicle_metadata, alert_configurations, route_waypoints, audit_logs, user_preferences" — that's over-modeling.

**Endpoints follow user actions.** Every API route maps to something a user does or something the system does on a schedule. If nobody calls it, it shouldn't exist.

**Components follow the design system.** Not "use the right colors" — build with the direction's character from the first line. The fleet dashboard is dense and operational from the first component. The recipe app is warm and generous from the first component. The design direction is not applied — it's embedded.

**Every visual element earns its place.** A button exists because there's an action the user needs to take. A card exists because there's a bounded piece of information to present. An icon exists because it communicates faster than text. If the element is there because "dashboards have cards" or "forms have labels", challenge it.

## 3. Intelligence Is Transparent

For products with LLM calls, workflows, or agents:

**Prompts are first-class.** Every prompt lives in a discoverable location (not inline in business logic). Each prompt has a clear purpose documented alongside it. If the prompt affects the user's experience, the user can see it and understand what it does.

**Show the work.** When the system is thinking, say what it's doing. "Checking 3 sources..." not "Loading...". "Matching recipes from your pantry..." not "Processing...". Transparency builds trust and tolerance for latency.

**Structured in, structured out.** Every LLM call has schema-validated input and output. No free-form prompting in production code. The schema IS the contract.

**Graduation awareness.** Build with the implementation level from the strategy, but leave markers for when it should change. A comment in code: `// via LLM call, graduate to template when patterns stabilize`. The build knows that today's approach is not permanent.

## 4. Security Is Not Optional

Not a checklist — a posture. The build assumes hostile input on every boundary.

**Auth where the strategy says auth.** If the product has users, every route that returns user data checks the session. One middleware, not scattered checks. Use the stack's auth service — never custom.

**RLS on user data.** If the product uses a database with user data, row-level security is on by default. Not "we'll add it later." Not "it's an internal tool." From day one.

**Secrets in env, never in code.** Validated at startup. Missing secret = fail fast with a clear message. Never silently fall back.

**Validate at boundaries.** User input, API responses, webhook payloads, agent outputs. Trust nothing from outside. Use schema validation (zod or equivalent). Inside the boundary, trust the types.

## 5. Experience Is Built In

The target feeling (defined in strategy, refined in design) guides every visible piece. A product that is functionally correct and experientially dead has missed the point — EIID restructures software around how intelligence flows, and experience is how users perceive that flow.

**Experience patterns are not polish.** Micro-interactions, transitions, gratification moments, restraint — these are built into the code from the first component, not applied as a final pass. A loading state that says "Checking 3 sources..." is built alongside the API call, not added later. A success animation on task completion is part of the task component, not a separate ticket.

**The absence test runs continuously.** For every element on screen: would the target feeling survive without it? If yes, remove it. This applies to animations too — don't animate what the user sees 50 times a day. Restraint is harder than addition, and more important.

**Gratification earns its place.** Not every action gets a celebration. Saving a form is a subtle check. Completing onboarding is a moment. Hitting a milestone is a delight. The scale of feedback matches the significance of the achievement.

**The user sees:**
- Screens that feel intentional, not generated
- Feedback that responds to their actions with appropriate weight
- Transitions that create continuity, not jumps
- Silence when nothing requires attention
- Speed that respects their time

**The user never sees:**
- The directory structure, the test suite, the logging format, the migration files

Build the invisible parts to be correct. Build the visible parts to be felt.

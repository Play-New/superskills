# Concepts

Canonical definitions. Every concept defined once. Commands and skills reference this file instead of re-explaining.

---

## EIID

Four layers that restructure every product around how intelligence flows from raw data to user value. Every component traces to exactly one layer.

**Enrichment** — where data enters. Not just databases and APIs: photos, voice notes, chat messages, forwarded emails, spreadsheets. People should not change how they work. Enrichment accepts data from every channel they already use and normalizes it for inference.

**Inference** — pattern detection, prediction, anomaly flagging. The compute is cheap. The hard part is knowing what questions to ask.

**Interpretation** — turns raw inference into something a person can act on. "Anomaly score 0.87" means nothing. "Orders dropped 30%, likely due to budget freeze, renewal in 45 days" is an insight. Context, comparison, explanation, recommended action.

**Delivery** — returns results through the same channels people already use. The person who sent a voice note gets the answer on WhatsApp. Triggered by conditions: threshold crossed, schedule, event, user request. The critical design choice is timing.

The web interface, when one exists, serves two roles: visualizations that don't fit in a message and configuration of the invisible layer. It is not the primary input surface.

## Implementation Levels

Five levels, ordered by complexity and cost:

- **LLM call** — single prompt-response. Classification, extraction, summarization. Cheapest intelligence unit.
- **Workflow** — fixed sequence of steps, some using LLM calls. Orchestration in code, intelligence in individual nodes.
- **Agent** — autonomous loop with tool use. The path emerges from context.
- **Code** — deterministic, high volume, low latency, auditable. Free at scale.
- **Buy** — commodity service. Costs less than building or prompting.

Each component records its level in the Approach field: `[Automate / Differentiate / Innovate] via [level]. Graduation: [trigger]`.

## Graduation

Implementation levels are not permanent. Start from the simplest level that works. An LLM call that covers 90% of cases beats an agent that covers 100% at 10x the cost. Graduate up (LLM call to workflow to agent) when edge cases justify complexity. Graduate down (agent to workflow to code) when patterns stabilize and volume demands it. Each component documents its graduation trigger.

## Rule Zero

Before adding anything — a screen, a component, an endpoint, a table, a setting, a dependency, a prompt clause, a workflow step — ask:

1. Does this trace to the EIID mapping? If not, don't build it.
2. Is there a simpler way? Fewer screens. Fewer components. Fewer abstractions.
3. Does this earn its place? If removing it wouldn't hurt the outcome, remove it.

This applies to everything: data models, API routes, agent tools, prompt clauses, workflow steps, notification channels, visual surfaces. A product with 3 screens that each do one thing perfectly beats a product with 12. A prompt with 5 clauses that produce the right output beats one with 30.

## Target Feeling

The emotional state when the product works perfectly. Not "satisfied" or "happy" — specific: calm control, warm relief, precise confidence. Defined during strategy, it becomes the quality benchmark for every decision in design, build, and review. Every touchpoint the user perceives — a screen, a message, an agent response, a CLI output — either contributes to the target feeling or undermines it.

## Experience Patterns

Six concrete behaviors that produce the target feeling. These apply to every modality — visual, conversational, CLI, agent, workflow, notification. Experience is not UI.

**Feedback** — every user action gets acknowledgment. The modality determines the form: visual surfaces use animation, conversational channels use status messages, CLI uses progress indicators, notifications use delivery confirmation. The principle is universal; the implementation varies.

**Pacing** — the product's rhythm matches the target feeling. A precise tool is fast — answer first, detail on demand. A warm product is gentle — context then decision. In agents: lead with insight, offer reasoning on request. In workflows: status at meaningful steps, not just at completion.

**Voice** — one personality across all text surfaces. Prompts, error messages, agent responses, notifications, empty states, CLI output, email subjects. A warm product is warm in its error messages. A precise product is precise in its WhatsApp replies.

**Gratification** — proportional to achievement. Routine saves are subtle. Meaningful milestones are moments. Per-modality: visual celebration, conversational warmth, CLI summary with delta, notification subject line that IS the win.

**Restraint** — what is deliberately absent. No confirmation dialogs for reversible actions. No "Is there anything else?" after every answer. No notification for routine completions. No explanation unless asked. No prompt clause that doesn't change the output. Silence is a feature.

**Absence test** — for every element the user perceives across any modality — a screen element, a message, a prompt clause, a workflow notification, a CLI line, an agent response — remove it. If the target feeling survives without it, the element should not exist.

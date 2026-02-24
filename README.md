# SuperSkills

Five autonomous skills for AI-native product development. A Claude Code plugin.

No dependencies. No build step. Pure markdown. Small enough for Claude to read entirely.

## What It Does

SuperSkills gives structure to AI-native product development. It maps business problems to four layers (Enrichment, Inference, Interpretation, Delivery), then enforces security, design, testing, and performance standards through autonomous hooks.

User flow: create a folder, drop in documents, install the plugin, open Claude Code. `/superskills:start` reads everything, maps to EIID, writes CLAUDE.md. Hooks handle the rest.

## Install

```bash
claude plugin install superskills
```

Or load locally during development:

```bash
claude --plugin-dir ./superskills
```

## Commands

| Command | What It Does |
|---------|-------------|
| `/superskills:start` | Read docs, map to EIID, recommend stack, write CLAUDE.md |
| `/superskills:strategy` | First run: validate EIID, set priorities. After: alignment + opportunities |
| `/superskills:trust` | First run: configure auth + data sensitivity. After: OWASP + GDPR audit |
| `/superskills:design` | First run: brand + tokens + shadcn. After: 5 hard rules + a11y |
| `/superskills:test` | First run: vitest + Playwright. After: run suite, block on failure |
| `/superskills:perf` | First run: set budgets. After: bundle, CWV, N+1, API costs |
| `/superskills:evolve` | Scan deps, detect drift, propose constraints and MCP connectors |
| `/superskills:handoff` | Generate HANDOFF.md with project state and findings |

Each command is adaptive. It detects whether setup has been done and switches between init and review mode automatically.

## Automatic Behavior

Hooks fire without user intervention:

- **PreToolUse(Bash):** security gate on shell commands (15s timeout)
- **PostToolUse(Write|Edit):** security gate on file changes (15s timeout)
- **Stop:** two agents run before task completion:
  1. **Test gate** runs the test suite. Blocks if tests fail.
  2. **Audit** checks trust (7 items), strategy alignment, and design rules on changed files. Advisory only, appends findings to CLAUDE.md.

The EIID awareness skill fires during plan mode. It checks each proposed change against the EIID mapping, flags scope creep, and surfaces missed opportunities. One line per observation, only when relevant.

## The EIID Framework

Four layers for AI-native products:

**Enrichment.** Connect data sources, fill gaps, normalize formats. Where raw material enters the system.

**Inference.** Detect patterns, predict outcomes, flag anomalies. The processing layer that AI has commoditized.

**Interpretation.** Turn raw inference into actionable insights. The layer that adds context: comparison, trend, explanation, recommendation.

**Delivery.** Push insights where people are, triggered by the right conditions. Where value concentrates.

## Recommended Stack

`/superskills:start` recommends this for new projects. Audit commands detect what is actually installed and adapt.

| Always | Role |
|--------|------|
| Supabase | Database, auth, storage, embeddings |
| Vercel | Hosting, edge functions |
| Inngest | Workflows, cron, retry |
| Next.js | Frontend, Server Components default |
| shadcnblocks > shadcn > Tailwind | UI hierarchy enforced by /superskills:design |

| Conditional | When |
|-------------|------|
| Brevo | Email/SMS/WhatsApp delivery |
| Telegram/Slack/Discord | Messaging delivery |
| Apify/Supermemory/Playwright | Enrichment, scraping |

CLI over MCP. MCP servers are data connectors, not skill replacements.

## Design Rules

Five hard rules enforced by `/superskills:design` and the stop audit:

1. shadcnblocks FIRST (1351 blocks, 90+ categories)
2. shadcn base SECOND
3. No custom CSS classes
4. All colors from design tokens (no hardcoded hex/rgb/hsl)
5. No arbitrary Tailwind values (no -[...])

## Trust Rules

`/superskills:trust` blocks on five categories. Everything else is advisory.

- Credentials hardcoded in source
- SQL injection
- XSS
- Auth bypass on protected routes
- PII exposure in logs, errors, or URLs

## Plugin Structure

```
superskills/
├── .claude-plugin/
│   └── plugin.json
├── commands/              8 user-invocable commands
│   ├── start.md
│   ├── strategy.md
│   ├── trust.md
│   ├── design.md
│   ├── test.md
│   ├── perf.md
│   ├── evolve.md
│   └── handoff.md
├── skills/                1 auto-invoked skill
│   └── eiid-awareness/
│       └── SKILL.md
├── agents/                2 stop-hook agents
│   ├── stop-tests.md
│   └── stop-audit.md
├── hooks/
│   └── hooks.json
└── README.md
```

15 markdown files + 2 JSON + README. Each command under 3K tokens. The entire plugin fits in one context window.

## Conceptual References

Three ideas shape the design.

**Value mapping (Simon Wardley).** Components evolve from genesis to commodity. Discovery assesses what is ready for automation (commodity) vs. what needs human judgment (genesis). The strategic question is not "should we use AI" but "which components has AI pushed from custom to commodity, and what does that free us to build above?"

**Value movement (Sangeet Paul Choudary).** AI commoditizes processing. Value moves to orchestration and delivery. When knowledge becomes abundant, value migrates to whoever manages the new dominant constraints: risk and coordination. EIID ends with Delivery because that is where value concentrates. The question is not "what can AI automate?" but "which workflows no longer need to exist?"

**Intelligence where the user is (Peter Steinberger).** Tools in existing workflows get adopted. Tools requiring context-switching get ignored. SuperSkills runs inside Claude Code. Intelligence goes to the user. The user does not go to intelligence.

## License

MIT

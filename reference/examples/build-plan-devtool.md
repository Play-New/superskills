# DepWatch — Build Plan

No visual UI. CLI + Slack + GitHub. Single LLM call for interpretation.

## What This Product Needs (derived from strategy)

- **Auth:** no — CLI tool, API keys in config file
- **Schema:** no — no database, reads repo state on demand
- **Layout shell:** no — no visual interface
- **Settings:** config file only — `.depwatch.json` in home directory. Repos to watch, severity thresholds, Slack webhook.
- **Visual surfaces:** no — CLI tables, Slack messages, GitHub comments
- **LLM call:** yes, one — risk summarization from structured diff. Schema-validated input and output.

## What This Product Does NOT Need

- Web UI. This is a CLI tool for developers who live in terminal and Slack.
- Database. State is in GitHub repos and npm registry. DepWatch reads, never stores.
- User accounts. Config is local. No server.
- Dashboard. Output is a table in the terminal.
- Real-time. Runs on demand or cron. Not a service.

## Build Order

1. **Scaffold** — CLI entry point with subcommands (scan, watch, config, detail, migrate). No framework, just Node.js.
2. **Config** — `.depwatch.json` management. Add/remove repos, set thresholds, Slack webhook. Validate on read.
3. **Enrichment** — GitHub repo scanner (read package.json, lockfiles), npm registry client (versions, changelogs, type signatures). Rate limit management.
4. **Inference** — semver analyzer, type signature comparator, changelog parser. Three independent analyzers, all pure code.
5. **Interpretation** — LLM call on structured diff. Input: JSON with changelog, type changes, affected imports. Output: schema-validated risk assessment. Prompt in dedicated file with graduation marker.
6. **Delivery** — CLI table formatter (80-char width, color with fallback), Slack DM for urgent alerts, GitHub issue commenter.
7. **Subcommands** — `scan` (full scan → table), `watch` (scan + Slack), `config` (manage settings), `detail` (deep dive on one dep).

## Key Design Decisions in Build

- No design system needed, but message structure is designed: lead with the risk level, then affected repos, then details. Same structure in CLI, Slack, and GitHub.
- Exit codes are meaningful: 0 = no breaking changes, 1 = breaking changes found. Scripts and CI use this.
- The LLM call has one prompt file with a clear graduation marker: `// graduate to template when patterns stabilize across 50+ summaries`. When risk summaries become predictable, the LLM call becomes a string template.
- 80-char terminal width is a hard constraint. Tables adapt, not overflow.

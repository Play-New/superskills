# DepWatch — Build Plan

Example build plan produced by `/super:build` init mode for a no-visual-UI devtool. Single LLM call for interpretation with graduation trigger. CLI + Slack + GitHub delivery only.

## Readiness

- **Strategy gate:** PASS — CLAUDE.md has EIID mapping with Approach fields on all layers
- **Stack gate:** PASS — Node.js CLI, GitHub API, npm registry API, Slack Web API
- **Design gate:** SKIP — no visual layers in EIID Interface Map. Design system covers message structure only.

## Build Order

### Shared Standards

| # | Piece | Type | Dependencies |
|---|-------|------|--------------|
| 1 | Project scaffold — CLI entry point, subcommands structure | shared-standard | none |
| 2 | Config management — .depwatch.json in home directory | shared-standard | #1 |
| 3 | Error handling — CLI-friendly errors (stderr, exit codes) | shared-standard | #1 |
| 4 | Logging — structured JSON to file, minimal stderr for user | shared-standard | #1 |

No auth, no database, no layout shell, no settings UI — this product has none of those needs.

### EIID Layers

| # | Piece | Type | EIID Layer | Approach | Dependencies |
|---|-------|------|------------|----------|--------------|
| 5 | GitHub repo scanner — read package.json and lockfiles from monitored repos | unique | Enrichment | Automate via code | #2, #4 |
| 6 | npm registry client — version metadata, changelogs, type signatures | unique | Enrichment | Automate via code | #4 |
| 7 | Semver analyzer — breaking change detection, minor version breaking changes | unique | Inference | Differentiate via code | #5, #6 |
| 8 | Type signature comparator — detect API surface changes across versions | unique | Inference | Differentiate via code | #6 |
| 9 | Changelog parser — extract breaking changes, migration notes, deprecations | unique | Inference | Differentiate via code | #6 |
| 10 | Risk summarizer — LLM call on structured diff, natural language risk assessment | unique | Interpretation | Differentiate via LLM call | #7, #8, #9 |
| 11 | CLI table output — scan results, risk levels, affected repos | unique | Delivery | Automate via code | #10 |
| 12 | Slack DM — urgent breaking change alerts | unique | Delivery | Automate via code | #10, #2 |
| 13 | GitHub issue commenter — repo-specific update comments on PRs | unique | Delivery | Automate via code | #10, #2 |

### CLI Subcommands

| # | Piece | Type | Dependencies |
|---|-------|------|--------------|
| 14 | `depwatch scan` — full scan of all monitored repos | unique | #7, #8, #9, #10, #11 |
| 15 | `depwatch watch` — scan + Slack notification for breaking changes | unique | #14, #12 |
| 16 | `depwatch config` — add/remove repos, set thresholds, Slack webhook | unique | #2 |
| 17 | `depwatch detail <dep>` — deep dive on a specific dependency | unique | #6, #7, #8, #9, #10 |

## Acceptance Criteria (examples)

**#2 Config management:**
- `depwatch config add-repo owner/repo` creates entry in ~/.depwatch.json
- `depwatch config set-slack https://hooks.slack.com/...` stores webhook URL
- Config file validates on read, fails with clear error on malformed JSON
- Missing config file on first run creates default with helpful comments

**#10 Risk summarizer (LLM call):**
- Input: structured JSON with changelog diff, type signature changes, affected import count
- Output: schema-validated JSON with risk_level (low/medium/high/critical), summary, affected_repos, migration_complexity
- Prompt stored in dedicated file (not inline in business logic)
- Works within 80-char terminal width
- Graduation marker in code: `// via LLM call, graduate to template when patterns stabilize across 50+ summaries`

**#11 CLI table output:**
- Table fits in 80-character terminal
- Columns: dependency, current, latest, risk, affected repos
- Risk levels color-coded (green/yellow/red) with fallback for no-color terminals
- Exit code 0 for no breaking changes, exit code 1 for breaking changes found

# Design System

## Direction
**Feel:** Senior teammate who flags what matters and stays quiet otherwise. Terse, scannable, actionable.
**Domain concepts:** Breaking change, migration complexity, risk level, affected repos, semver, dependency tree, production vs dev dependency
**Signature:** The risk bar. Three filled/empty squares (filled squares = higher risk) next to every dependency in CLI output. Visible at a glance, no color dependency.
**Rejected defaults:** Web dashboard with charts, color-coded dependency trees, interactive upgrade wizards

## Experience Patterns

**Feedback:** CLI — scan progress shows "Checking 142 dependencies..." with a counter that increments. Slack DM arrives only for breaking changes in production deps. GitHub comment appears when a PR touches an affected dependency. Every channel's feedback is proportional to the event's importance.
**Pacing:** Fast and direct — answer first, always. CLI output leads with the risk table, not a summary paragraph. Slack DM leads with severity and package name, not "DepWatch has detected an update." GitHub comment leads with risk level and migration estimate, details in a collapsible section.
**Voice and tone:** Terse like a senior teammate. No greetings, no sign-offs, no "I found that..." framing. CLI: `■■■ lodash 4.17→5.0 3 moderate`. Slack: `*breaking* lodash 4.17 → 5.0 — 3 repos affected`. The product never explains itself — it reports facts and suggests actions.
**Gratification:** Minimal. A clean scan shows "0 breaking changes" — that's the reward. Weekly summary leads with "No action needed this week" when applicable. No badges, no streaks, no progress indicators for dependency health. The gratification is trust: developers trust the output immediately.
**Restraint:** No Slack message for devDependency patch updates. No notification for resolved issues (the absence of the alert IS the resolution). No interactive CLI prompts during scan (breaks CI/cron). No color as primary information carrier (risk squares work in monochrome). Weekly batching for non-breaking updates — most weeks, DepWatch is silent.

## EIID Interface Map

| Layer | Modality | Surface | Rationale |
|-------|----------|---------|-----------|
| Enrichment | automated | GitHub API + npm registry | No user interaction needed during scanning. |
| Inference | automated | internal pipeline | Breaking change detection runs without user input. |
| Interpretation | conversational | Slack DM + CLI output | Risk summaries fit in a message. No spatial data to visualize. |
| Delivery | notification | Slack + CLI + GitHub comments | Developers are already in these channels. |

**Visual scope:** None. No EIID layer requires a graphical interface. All information fits in text.
**Non-visual scope:** All layers are automated, conversational, or notification-based. Design covers message structure, formatting conventions, and channel-specific adaptation only.

## References
**Borrowed:** Slack message density from Sentry alerts (severity + key fact + one action). CLI table compactness from `npm outdated` (aligned columns, no decoration). GitHub comment structure from Dependabot (collapsible details, migration command).
**Avoided:** Verbose changelogs pasted into Slack (information overload). Color-coded terminal output as primary information carrier (inaccessible). Interactive CLI prompts during scan (breaks CI/cron usage).
**Assets:** None.

## Conversational Patterns

No layout, typography scale, color tokens, or composition rules. The design system for DepWatch is entirely about how information is structured in text.

### Slack DM (urgent notifications)
```
*breaking* `lodash` 4.17 → 5.0 — 3 repos affected
Type signature changes in `_.merge`. Migration: moderate.
Repos: project-a, project-b, project-c
→ Run `depwatch detail lodash` for full diff
```

### CLI Output (full scan)
```
RISK  PACKAGE        UPDATE     REPOS  MIGRATION
■■■   lodash         4.17→5.0   3      moderate
■■□   express        4.18→5.0   2      simple
■□□   typescript     5.3→5.4    5      trivial
□□□   prettier       3.1→3.2    4      none
```

### GitHub Issue Comment (repo-specific)
```markdown
## Dependency Update: lodash 4.17 → 5.0

**Risk:** High — type signature changes in `_.merge`, `_.cloneDeep`
**Migration complexity:** Moderate (estimated 2-4 hours)
**Affected files:** 12 imports across 3 modules

<details><summary>Breaking changes</summary>

- `_.merge` no longer accepts `null` as first argument
- `_.cloneDeep` returns frozen objects by default
- `_.template` requires explicit delimiter option

</details>

Run `depwatch migrate lodash` for automated fixes.
```

### Channel Formatting Rules
- Bold for severity keywords and package names
- Monospace for versions, commands, file paths, and function signatures
- Plain text for descriptions and rationale
- Links only for drill-down actions (never decorative)
- No color in CLI output (risk squares work in monochrome). Color is optional enhancement, not information carrier.

### Timing Rules
- Breaking changes in production dependencies: immediate Slack DM
- Non-breaking updates: batched into weekly CLI report
- GitHub issue comments: created when a PR touches an affected dependency
- No notification for devDependency patch updates (noise reduction)

## Decisions
| Decision | Why | Date |
|----------|-----|------|
| No web dashboard | EIID Interface Map shows all layers are text-compatible. Risk summaries fit in a Slack message. Dependency lists fit in a CLI table. A web dashboard adds infrastructure cost for information already accessible where developers work. | Mar 2026 |
| Risk squares over color coding | CLI output must work in 80-char monochrome terminals. Filled/empty squares communicate severity without color, without width, without Unicode beyond basic block characters. | Mar 2026 |
| Immediate DM only for production breaking changes | Most dependency updates are not urgent. Notifying on every minor patch trains developers to ignore the tool. Reserve the interrupt for updates that could break production. | Mar 2026 |

# DepWatch

## Business
**Client:** Open source project, MIT license
**Industry:** Developer tooling, no revenue model. Maintained by a small team.

## User
**End user:** Senior developer maintaining 5+ repositories, lives in terminal and Slack, checks dependencies weekly
**Need:** Know which dependency updates will break things before merging, not after

## Stack
Node.js CLI tool. GitHub API (repo scanning), npm/yarn registry APIs (version data), Slack Web API (notifications). No frontend framework. No database. Reads repo state on demand.

## EIID

### Enrichment (collect)
**Have:** GitHub API access to monitored repos, npm/yarn registry for version metadata
**Human input:** None during operation. User configures watched repos and severity thresholds once via CLI.
**Missing:** Private registry support, monorepo workspace detection
**Connect:** GitHub API for package.json and lockfile reading, npm registry for version metadata and changelogs
**Approach:** Automate — standard API consumption. No competitive advantage in how data is fetched.

### Inference (patterns)
**Detect:** Breaking changes via semver analysis, changelog keyword parsing, TypeScript type signature comparison
**Predict:** Migration complexity based on number of affected imports, depth of API surface change, availability of codemods
**Flag:** Production dependency updates with major version bumps or known breaking changes
**Approach:** Differentiate — the quality of breaking change detection is the product value. Semver alone misses breaking changes in minor versions. Changelog parsing and type comparison catch what semver does not.

### Interpretation (insights)
**Surface:** "lodash 4.17 to 5.0: type signature changes in _.merge. 3 repos affected. Migration: moderate."
**Frame as:** Risk level, affected repo count, migration complexity estimate, specific breaking changes listed
**Approach:** Differentiate — framing risk clearly saves developer time. A version bump number alone does not communicate effort.

### Delivery (reach)
**Channels:** Slack DM (urgent: breaking changes in production deps), CLI table output (full scan results), GitHub issue comment (repo-specific updates)
**Triggers:** Breaking change detected in production dependency, weekly scan completed, PR touches affected dependency
**Timing:** Slack DM immediately on detection. CLI report on demand or weekly cron. GitHub comment when PR is opened.
**Approach:** Automate — Slack, CLI, and GitHub APIs are commodity delivery channels.

## Technology Constraints
Use npm registry API, NOT scraping npmjs.com.
GitHub API rate limit: 5000 requests/hour authenticated. Batch repo scanning to stay under 50% of limit.
Slack message size limits: 3000 chars per block, 50 blocks per message.
CLI output must work in 80-character terminals.

## Code Architecture
- CLI entry point with subcommands: scan, watch, config, detail, migrate
- Shared core module for registry queries, diff analysis, and risk scoring
- Channel adapters: Slack, CLI table formatter, GitHub issue commenter
- Configuration stored in .depwatch.json in user home directory
- No daemon process. Runs on demand or via external cron (CI, crontab).

## Design System
**Framework:** None. No visual interface.
**Token source:** None.
**Direction:** Developer-native communication. Message structure and formatting only. Terse, scannable, actionable.

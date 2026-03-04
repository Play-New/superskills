# Architecture Decisions

### Mar 1, 2026 — No Web Dashboard
**Type:** decision
**Summary:** EIID Interface Map shows all layers are automated, conversational, or notification-based. Risk summaries fit in a Slack message. Dependency lists fit in a CLI table. Building a web dashboard would add infrastructure cost and maintenance burden for information that is already accessible where developers work. The three existing channels (Slack, CLI, GitHub comments) cover all use cases without a browser.
**EIID Layer:** interpretation, delivery
**Action:** No web framework dependency. No hosting infrastructure. Tool remains a pure CLI with outbound notifications.

### Mar 2, 2026 — Slack Over Email for Urgent Notifications
**Type:** decision
**Summary:** Developers respond to Slack in minutes, email in hours. Breaking change alerts lose value with delay. A lodash major version bump detected at 10am should reach the developer before they start a feature branch that depends on it, not after lunch when they check email. Slack DMs also support threading for follow-up discussion about migration strategy.
**EIID Layer:** delivery
**Action:** Slack Web API for urgent breaking change notifications. Email not implemented.

### Mar 2, 2026 — GitHub Issue Comments Over PR Comments
**Type:** decision
**Summary:** Issues persist and can be referenced long after creation. PR comments disappear when the PR is merged or closed. Dependency updates affect the repo for months or years, not just the lifespan of one PR. An issue titled "lodash 5.0 migration" becomes a trackable work item. A PR comment becomes invisible history.
**EIID Layer:** delivery
**Action:** GitHub issue creation via API when breaking change affects a specific repo. Link to issue from Slack DM for cross-channel continuity.

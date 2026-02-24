---
name: stop-audit
description: Pre-stop audit. Trust, strategy, and design checks on changed files. Advisory only.
tools: Read, Glob, Grep, Write, Edit
---

Three checks on files changed in this session. For each item: check, report, move on. Do not deliberate.

## Trust Deep Scan

Scan all files changed in this session. For each item answer PASS or FAIL with file:line if FAIL:

1. Hardcoded API keys, passwords, tokens, secrets?
2. SQL injection (unsanitized user input in queries)?
3. XSS (unescaped user content rendered as HTML)?
4. Auth bypass (missing auth checks on protected routes)?
5. PII exposure (personal data in logs, errors, URLs)?
6. Missing RLS policies on Supabase tables?
7. Secrets in environment config committed to git?

## Strategy Alignment Check

Read CLAUDE.md for the EIID mapping. Compare recent changes against it:

1. Which EIID layer does each change support? (enrichment / inference / interpretation / delivery / none)
2. Any file or dependency not traceable to the EIID mapping? Flag as scope creep.

Do not suggest new opportunities. That is for `/superskills:strategy`.

## Design Audit

Scan all .tsx files changed in this session. For each rule answer PASS or FAIL with file:line if FAIL:

1. shadcnblocks FIRST: Any UI section replaceable by a shadcnblocks block?
2. shadcn base SECOND: Any custom component duplicating shadcn?
3. NEVER custom CSS classes: Any non-Tailwind class names?
4. Global token file required: Any hardcoded hex/rgb/hsl in components?
5. Zero inline arbitrary values: Any Tailwind arbitrary syntax (-[...])?

Also check: color contrast >= 4.5:1, focus states, alt text, form labels.

## Report

Read CLAUDE.md, then append all findings using the Edit tool to the relevant sections (Security Findings, Architecture Decisions, Design Findings).

Advisory only. Respond `{"ok": true}` when done.

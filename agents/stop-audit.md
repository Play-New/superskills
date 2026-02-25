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

Do not suggest new opportunities. That is for `/super:strategy`.

## Design Audit

Scan all component files changed in this session (.tsx, .jsx, .vue, .svelte). For each rule answer PASS or FAIL with file:line if FAIL.

**Universal rules (always apply):**
1. Color contrast >= 4.5:1 for normal text, 3:1 for large text
2. Focus states on interactive elements
3. Alt text on images
4. Labels on form inputs
5. No hardcoded color values (hex, rgb, hsl) in components — use design tokens
6. `cursor-pointer` on all clickable elements (buttons, links, cards with onClick, toggles, tabs)
7. No Unicode escape sequences for accents — use UTF-8 directly (`è` not `\u00e8`)

**Framework-specific rules (detect from package.json):**
- shadcn + Tailwind: check shadcnblocks registry via CLI before custom sections, shadcn base second, no custom CSS classes, no arbitrary Tailwind values
- Chakra/MUI/Mantine: use framework components before custom, styling through framework APIs
- Tailwind only: no custom CSS classes, no arbitrary values
- No framework: skip framework-specific checks

## Report

Read CLAUDE.md for project context. Write findings to `.superskills/`:

- **Replace** Security Findings and Design Findings in `.superskills/report.md` (each audit is a snapshot).
- **Append** strategy alignment findings to `.superskills/decisions.md`.
- Update status counts at the top of `.superskills/report.md`.
- If Project Profile exists in report.md, update recurring patterns.

Advisory only. Respond `{"ok": true}` when done.

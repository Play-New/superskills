---
name: build-awareness
description: Build quality awareness during implementation. Fires when Claude writes or edits source code. Checks shared standards compliance, test coverage, EIID traceability, and strategy alignment.
user-invocable: false
---

When writing or editing application source code (not config, not tests, not markdown):

1. Read CLAUDE.md for the EIID mapping. Read `reference/shared-standards.md` for building block patterns. Read `.superskills/decisions.md` for build history (if it exists).

2. **Shared standard check:** if the change touches auth, settings, logging, error handling, database schema, or layout shell, check against the corresponding section in `reference/shared-standards.md`. Flag deviations:
   - Auth middleware missing on a new protected route
   - Error response not using the structured format (`{ error, code? }`)
   - Logging with console.log instead of the structured logger
   - Settings change that doesn't persist or validate
   - Database table missing RLS, timestamps, or indexes on filtered columns
   - Layout component using arbitrary values instead of design tokens

3. **Test coverage check:** does the code being written or edited have corresponding tests? Not line coverage — behavior coverage. If a new API route is added, is there a test that calls it with valid input and gets expected output? If a new component renders data, is there a test that verifies it renders? Flag untested new behavior.

4. **EIID traceability:** which EIID layer does this code serve? If it doesn't trace to any layer AND it's not supporting infrastructure (tests, types, config, utilities used by EIID code), flag: "This code doesn't trace to the EIID mapping. Is it supporting infrastructure or scope creep?"

5. **Implementation level match:** if CLAUDE.md says a component is "via code" and the implementation uses an LLM call, flag the mismatch. If it says "via buy" and the code is building it custom, flag. If it says "via agent" and the code is a fixed pipeline with no iteration, flag.

6. **Prompt hygiene:** if the code contains LLM prompts (system messages, user templates, tool descriptions):
   - Is the prompt stored in a discoverable location (not buried in a function)?
   - Does the prompt have schema validation on its output?
   - Is the prompt accessible to the settings/prompt management UI?
   - Flag inline prompts in business logic: "Move this prompt to a dedicated prompt file or config."

7. **Regression awareness:** if the change modifies existing behavior (not adding new), check: are existing tests still relevant? Will they catch a regression from this change? If the change is a refactor, the tests should pass without modification. If a test needs updating to match the new behavior, that's a signal the behavior changed — make sure it was intentional.

One line per observation. Only when relevant. Not blocking. Brief.

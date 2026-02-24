---
description: Security. First run configures auth and data sensitivity. After that, OWASP + GDPR audit.
allowed-tools: Read, Glob, Grep, Write, Edit, Bash
---

# Trust

## Detect Mode

Read CLAUDE.md. If Security Findings section has content, run **audit mode**. Otherwise, run **init mode**.

---

## Init Mode

1. Ask about authentication requirements:
   - Auth method (Supabase Auth, Clerk, NextAuth, Lucia, custom)
   - Protected routes
   - Role-based access needs
2. Ask about data sensitivity:
   - PII fields to protect
   - Data retention requirements
   - GDPR needs (consent, export, deletion)
3. Configure security foundations:
   - RLS policies for Supabase tables
   - Auth middleware for Next.js
   - Security headers in next.config
   - Environment variable validation
4. Write initial security posture to CLAUDE.md Security Findings.

---

## Audit Mode

### OWASP Top 10

Scan all source files. For each item: PASS or FAIL with file:line.

1. **Broken access control.** API routes without auth checks? Missing RLS?
2. **Injection.** Unsanitized user input in queries, commands, templates?
3. **XSS.** Unescaped user content rendered as HTML?
4. **SSRF.** User-provided URLs fetched server-side without validation?
5. **Security misconfiguration.** Debug mode on? CORS too permissive? Missing headers?
6. **Vulnerable components.** Run `npm audit`. Flag critical/high.
7. **Auth failures.** Weak session handling? Missing rate limiting?
8. **Data integrity.** Missing input validation on Server Actions or API routes?
9. **Logging failures.** Security events not logged? PII in logs?
10. **SSRF.** Server-side requests to user-controlled URLs?

### Secrets Scan

- Hardcoded API keys, passwords, tokens in source files
- `.env` or `.env.local` in .gitignore?
- Secrets in git history (search for common patterns)

### GDPR Compliance

- Consent mechanism present?
- Data export capability?
- Data deletion capability?
- Data minimization practiced?
- Retention policy defined?
- Breach notification process documented?

### Stack-Adaptive Checks

Detect from package.json. Apply only relevant sections.

**Supabase:**
- RLS enabled on all tables?
- service_role key server-side only?
- Auth middleware on protected routes?
- Storage policies configured?

**Vercel:**
- Env vars split between Preview and Production?
- Edge middleware for auth?
- Security headers configured?
- No secrets in vercel.json?

**Inngest:**
- INNGEST_SIGNING_KEY validated?
- No PII in event payloads?
- Steps idempotent?

**Next.js:**
- Server Actions validate input?
- API routes check auth?
- Dynamic route params sanitized?
- No path traversal in file operations?

### Blocking Rules

**BLOCK** (must fix before proceeding):
- Credentials hardcoded in source
- SQL injection
- XSS
- Auth bypass on protected routes
- PII exposure in logs, errors, or URLs

Everything else: severity rated HIGH / MEDIUM / LOW.

### Output

Append to CLAUDE.md Security Findings:

| Severity | File:line | Issue | Fix |
|----------|-----------|-------|-----|
| BLOCK/HIGH/MEDIUM/LOW | path:line | description | what to do |

# Security Audit Guide

Full security checklist for the review command. Apply all checks relevant to the detected stack. Scan all source files for each item.

## OWASP Top 10

For each item: PASS or FAIL with file:line.

1. **Broken access control.** API routes without auth checks? Missing RLS? Server actions callable without session validation?
2. **Injection.** Unsanitized user input in queries, commands, templates? String concatenation in SQL/NoSQL queries instead of parameterized queries?
3. **XSS.** Unescaped user content rendered as HTML? `dangerouslySetInnerHTML` or equivalent without sanitization?
4. **SSRF.** User-provided URLs fetched server-side without validation? Missing allowlist for external requests?
5. **Security misconfiguration.** Debug mode on? CORS too permissive? Missing security headers (CSP, X-Frame-Options, X-Content-Type-Options)?
6. **Vulnerable components.** Run `npm audit`. Flag critical/high. Check for outdated dependencies with known CVEs.
7. **Auth failures.** Weak session handling? Missing rate limiting on login/signup? No account lockout after failed attempts?
8. **Data integrity.** Missing input validation on Server Actions or API routes? No schema validation (zod, yup, etc.) on user-facing endpoints?
9. **Logging failures.** Security events not logged? PII in logs? No audit trail for admin actions?
10. **CSRF.** State-changing actions without CSRF tokens? Missing SameSite cookies? GET requests that mutate state?

## Secrets Scan

- Hardcoded API keys, passwords, tokens in source files
- `.env` or `.env.local` in .gitignore?
- Secrets in git history (search for common patterns: `sk-`, `AKIA`, `ghp_`, `password =`, `secret =`)
- Service account JSON files committed to the repo
- Private keys or certificates in the source tree

## GDPR Compliance

Six checks:
1. Consent mechanism present before collecting personal data?
2. Data export capability (user can download their data)?
3. Data deletion capability (user can request account and data removal)?
4. Data minimization practiced (only collecting what's needed)?
5. Retention policy defined (how long is data kept)?
6. Breach notification process documented?

## Stack-Adaptive Checks

Detect from package.json. Apply checks for each detected tool. For tools not listed below, identify their security surface and apply equivalent checks.

### Database with auth (Supabase, Firebase, etc.)

- Row-level security or equivalent authorization rules enabled on all tables with user data?
- Admin/service keys server-side only? Never exposed to the client bundle?
- Auth middleware on protected routes?
- Storage/bucket policies configured (no public-write on sensitive buckets)?
- Realtime subscriptions respect RLS? Clients cannot subscribe to other users' data?

### Hosting platform (Vercel, Netlify, etc.)

- Env vars split between preview and production?
- Security headers configured (CSP, HSTS, X-Frame-Options)?
- No secrets in deployment config files (vercel.json, netlify.toml)?
- Preview deployments password-protected or restricted?

### Workflow engine (Inngest, Trigger.dev, etc.)

- Signing keys validated on incoming webhooks?
- No PII in event payloads (use IDs, fetch data inside the function)?
- Steps idempotent (safe to retry without side effects)?
- Error handling does not leak internal state in responses?

### Framework (Next.js, Remix, Nuxt, etc.)

- Server actions/loaders validate input before processing?
- API routes check auth before returning data?
- Dynamic route params sanitized (no path traversal)?
- No file system operations using user-provided paths?
- Middleware runs on all protected route groups?

### Payment (Stripe, LemonSqueezy, etc.)

- Webhook signature verification on all payment events?
- Price/amount validated server-side (not trusted from client)?
- Subscription status checked before granting access?

## Blocking Rules

**BLOCK** (must fix before proceeding):
- Credentials hardcoded in source
- SQL/NoSQL injection
- XSS
- Auth bypass on protected routes
- PII exposure in logs, errors, or URLs

Everything else: severity rated HIGH / MEDIUM / LOW.

## Output Format

| Severity | File:line | Issue | Fix |
|----------|-----------|-------|-----|
| BLOCK/HIGH/MEDIUM/LOW | path:line | description | what to do |

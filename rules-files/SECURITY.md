# SECURITY.md — Secure Vibe Coding Reference

> **How this file is used:** This is the durable security reference for this project.
> AI coding tools load it as a reference via their configuration files (CLAUDE.md,
> AGENTS.md, etc.). Human developers should read it too — it defines the floor, not
> the ceiling, for every code change in this repo.
>
> **Update cadence:** Review when OWASP or MITRE publish new editions, or when your
> threat model materially changes. Don't touch it just because something was added to
> LESSONS.md.

---

## Non-Negotiable Rules

These apply to every code change, every file, every language. There are no exceptions
for "quick hacks," prototypes, or internal tools.

### Secrets

- Never hardcode API keys, passwords, tokens, connection strings, or credentials in source code
- Always use environment variables or a secrets manager (AWS Secrets Manager, GCP Secret Manager, HashiCorp Vault, Azure Key Vault)
- If you encounter a hardcoded secret in existing code, flag it immediately — do not proceed past it without surfacing it
- `.env` files are for local development only and must be in `.gitignore`; never commit them

### Injection Prevention

- Use parameterized queries or ORMs for all database interactions — never construct SQL via string concatenation
- Sanitize all HTML output and user-supplied input to prevent XSS
- Validate all inputs at the boundary — see Input Validation section below
- Apply the same discipline to OS commands, LDAP queries, and template rendering

### Least Privilege

- When writing or reviewing cloud/IAM configurations, propose the most restrictive permissions that allow the task to complete
- Explain what each permission grants — do not use wildcards (`*`) in production IAM policies without explicit justification
- Services should not have access to resources they don't use

### Error Handling

- Never leak stack traces, system paths, internal error details, or framework version information to the client
- User-facing error messages must be generic
- Log detailed context server-side only, structured as JSON for observability
- Handle errors explicitly — do not swallow exceptions silently

---

## OWASP Top 10 (2021) — Applied

These are the ten most critical web application security risks. Every item has a
concrete "what this means here" note.

| # | Risk | What This Means Here |
|---|------|----------------------|
| A01 | Broken Access Control | Every endpoint must verify authorization, not just authentication. Don't rely on "the UI hides it." Test that users cannot access other users' data by manipulating IDs or paths. |
| A02 | Cryptographic Failures | Use TLS everywhere. Don't store sensitive data you don't need. Use Argon2 or BCrypt for passwords — never MD5, SHA-1, or unsalted SHA-256. |
| A03 | Injection | Parameterized queries for all DB access. Sanitize all rendered HTML. Applies to SQL, NoSQL, OS commands, LDAP, and XML. |
| A04 | Insecure Design | Security requirements belong in design, not retrofit. For features touching auth, payments, or PII: design the threat model first, then write code. |
| A05 | Security Misconfiguration | Default credentials, open cloud storage, verbose error messages, unnecessary features enabled. Audit config with every deployment change. |
| A06 | Vulnerable Components | Before adding a dependency, verify it's actively maintained and has no critical CVEs. Run `npm audit` / `pip audit` / `gradle dependencyCheckAnalyze` in CI. Never use abandoned packages. |
| A07 | Auth & Session Failures | Use an established auth library — don't hand-roll authentication. Enforce MFA where possible. Rotate and expire tokens. Sessions must invalidate on logout. |
| A08 | Software & Data Integrity | Verify checksums for downloaded artifacts. Don't pull from untrusted sources in CI/CD. Treat deserialized data as untrusted input. |
| A09 | Logging & Monitoring Failures | Log auth events, access control failures, and input validation failures. Don't log sensitive data (passwords, tokens, PII). Ensure logs are queryable. |
| A10 | SSRF | Validate and allowlist URLs before making server-side requests. Reject requests to internal IP ranges (169.254.x.x, 10.x.x.x, etc.). |

---

## MITRE Top 25 CWEs — Priority Watch List

Focus on these when reviewing code that handles external input, authentication, or
data persistence. Full list at [cwe.mitre.org/top25](https://cwe.mitre.org/top25/).

| CWE | Name | Watch For |
|-----|------|-----------|
| CWE-787 | Out-of-bounds Write | Memory-unsafe languages; buffer operations |
| CWE-79 | Cross-site Scripting | Any rendered HTML from user input |
| CWE-89 | SQL Injection | Any DB query touching external input |
| CWE-416 | Use After Free | C/C++ and unsafe Rust |
| CWE-78 | OS Command Injection | `exec`, `shell`, `subprocess` calls |
| CWE-20 | Improper Input Validation | Every external boundary |
| CWE-125 | Out-of-bounds Read | Buffer reads in low-level code |
| CWE-22 | Path Traversal | File operations with user-supplied paths |
| CWE-352 | CSRF | State-changing requests without CSRF tokens |
| CWE-434 | Unrestricted File Upload | File upload endpoints — validate type, size, content |
| CWE-862 | Missing Authorization | Functions that forget to check permissions |
| CWE-476 | NULL Pointer Dereference | Unchecked null/undefined access |
| CWE-287 | Improper Authentication | Bypassable auth checks |
| CWE-190 | Integer Overflow | Math in loops, size calculations |
| CWE-502 | Unsafe Deserialization | JSON.parse of untrusted data, pickle, yaml.load |

---

## Input Validation

Every function that accepts external data must validate at the boundary. "External"
means: HTTP requests, message queue payloads, file uploads, environment variables
read at runtime, database reads of user-controlled fields, and IPC from other services.

**Choose validation tooling appropriate to your stack. Fill this in for your project:**

```
# Example — replace with your actual choices:
# TypeScript/Node.js:  Zod (preferred), Joi
# Python:             Pydantic (preferred), Marshmallow
# Go:                 validator package, hand-rolled with explicit checks
# .NET/C#:            FluentValidation, DataAnnotations
# Frontend forms:     Zod + react-hook-form, Yup
```

Validation must fail loudly and early. A validation failure is not a 500 — it's a
400 with a clear, non-leaking error message.

---

## Dependency Safety

Before suggesting or adding a new library:

1. Confirm it is actively maintained (recent commits, issues being responded to)
2. Check for critical CVEs — use [osv.dev](https://osv.dev) or `npm audit` / `pip audit`
3. Prefer well-known packages with large install bases over novel alternatives
4. If uncertain about a package's safety, say so explicitly — never guess or hallucinate package names

In CI, dependency scanning must run on every PR. Acceptable tools: `npm audit`,
Dependabot, Snyk, OWASP Dependency-Check, `pip-audit`.

---

## Feature Verification Checklist

Before closing out any major feature, confirm:

- [ ] CSRF / CORS protection configured
- [ ] Rate limiting applied to all public-facing endpoints
- [ ] Secure HTTP headers implemented (Helmet.js, `SecurityMiddleware`, or equivalent)
- [ ] Passwords hashed with Argon2 or BCrypt — never MD5 or SHA-1
- [ ] All external inputs validated at the boundary
- [ ] Secrets sourced from environment / secrets manager — never committed
- [ ] Client-facing error messages are generic and sanitized
- [ ] Auth endpoints have brute-force protection (rate limiting + lockout or CAPTCHA)
- [ ] File uploads validated for type, size, and content — not just extension
- [ ] Dependencies checked for critical CVEs

---

## What This Document Is Not

This document raises the floor. It does not install a ceiling. These rules make
the secure path the default path — they do not replace:

- A secrets scanner in pre-commit hooks (`gitleaks`, `detect-secrets`)
- SAST tooling in CI (`semgrep`, `eslint-plugin-security`, `bandit`)
- Code review that treats AI-generated code with the same scrutiny as human-written code
- A threat model appropriate to your application's actual risk profile
- Penetration testing for production systems handling sensitive data

A determined developer can override any AI suggestion. The AI is not deterministic
and will not catch every vulnerability. These files make mistakes less likely —
not impossible.

# CLAUDE.md — Project Context and Coding Standards

<!--
  TEMPLATE INSTRUCTIONS (remove this block before using):

  This file has two jobs:
  1. Tell Claude Code what this project is — stack, structure, constraints.
  2. Tell Claude Code how to work in this project — behavior, workflow, limits.

  Section 1 (Project Identity) is intentionally blank. Fill it in before you
  use this file. The security standards in Section 2 onward are pre-populated
  and apply universally — customize only where your project genuinely differs.

  Filling in Section 1 is the highest-leverage thing you can do with this file.
  Without it, Claude has no project context and will hedge across every ecosystem.
-->

---

## 1. Project Identity

> **Fill this in. This section is what makes the rest of the file work.**

```
Project name:     [YOUR PROJECT NAME]
Last updated:     [YYYY-MM-DD]
Owner:            [TEAM OR PERSON]

What it does:
  [One or two sentences. What problem does this solve? Who uses it?]

Primary stack:
  Language(s):    [e.g., TypeScript, Python, C#]
  Framework(s):   [e.g., Express, FastAPI, ASP.NET Core]
  Database(s):    [e.g., PostgreSQL, SQL Server, DynamoDB]
  Cloud:          [e.g., AWS, GCP, Azure — and which services matter]
  Frontend:       [e.g., React + Vite, Next.js, none]

Key directories:
  [e.g., src/api/ — HTTP handlers]
  [e.g., src/services/ — business logic]
  [e.g., src/db/ — database access layer]
  [e.g., infra/ — IaC (Terraform, CDK, etc.)]

Auth approach:
  [e.g., JWT via [library], sessions via [library], OAuth2 with [provider]]

Input validation library:
  [e.g., Zod, Pydantic, FluentValidation — so Claude stops hedging]

Hard constraints (things Claude must never do in this project):
  [e.g., Never modify migration files directly — use the migration CLI]
  [e.g., Never add npm dependencies without approval]
  [e.g., Never write to the database outside of src/db/]
```

---

## 2. Security Standards

All coding in this project follows the standards defined in [`SECURITY.md`](./SECURITY.md).

Claude must read `SECURITY.md` at the start of any session involving:
- Authentication, authorization, or session management
- Database access or query construction
- File uploads or external data ingestion
- Cloud/IAM configuration
- New dependencies

Do not reproduce the rules from `SECURITY.md` here. Reference it. If there is a
conflict between this file and `SECURITY.md`, `SECURITY.md` wins.

**Project-specific additions or overrides to SECURITY.md:**

```
[Add anything here that is specific to this project and not already in SECURITY.md.
Leave this blank if the universal standards apply without modification.]
```

---

## 3. Lessons

All accumulated project lessons are in [`LESSONS.md`](./LESSONS.md).

Load `LESSONS.md` before generating code in any area of the codebase that has
existing lessons. When you make a mistake that gets corrected, or when a pattern
is confirmed as the right approach, add an entry to `LESSONS.md` — don't just
acknowledge it in conversation where it will be lost.

---

## 4. How to Work in This Project

### Before Writing Code

For any change that touches auth, data access, payments, or file handling:
1. State what you're about to change and why
2. Identify the relevant SECURITY.md sections
3. Check LESSONS.md for applicable prior lessons
4. Then write the code

For routine changes, proceed directly — don't narrate the process.

### While Writing Code

- Use inline comments to explain *why* a security pattern is used, not just *what* it does
- If uncertain about a security recommendation, say so explicitly
- If a package you want to suggest doesn't appear in the existing `package.json` / `requirements.txt` / equivalent, flag it before adding it
- One clear warning per issue — don't repeat security concerns already surfaced

### After Writing Code

For any substantial code block touching security-relevant areas, append:

```
🔒 Security notes:
- [What was applied or confirmed, e.g., "Parameterized query used — no string concatenation"]
- [Any open question or risk the reviewer should verify]
```

This is not a self-certification. It's a handoff note for the human reviewer.
The audit starts here — it doesn't end here.

### When Asked for a "Quick Hack"

Warn once about the security implications. If the developer confirms, provide the
code with a comment marking it as intentionally non-standard and a TODO to address it.
Don't lecture. Don't repeat the warning.

---

## 5. Verification Checklist

Before closing out any major feature, confirm these are addressed.
Full checklist with context lives in `SECURITY.md`.

- [ ] CSRF / CORS configured
- [ ] Rate limiting on public endpoints
- [ ] Secure HTTP headers
- [ ] Passwords hashed with Argon2 or BCrypt
- [ ] Input validated at the boundary
- [ ] Secrets from env / secrets manager only
- [ ] Client errors sanitized

---

## 6. Communication Style

- Lead with the code or answer. Follow with security notes.
- Be direct and concise. No preamble.
- If speculating, say so.
- Do not be preachy — one warning per issue is enough.

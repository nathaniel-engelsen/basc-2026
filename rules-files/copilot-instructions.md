# GitHub Copilot Instructions

This project enforces security-first coding standards. The authoritative reference
is `SECURITY.md` in the project root. Accumulated project lessons are in `LESSONS.md`.

## Always

- Never output hardcoded secrets, API keys, tokens, or passwords. Use environment
  variables or a secrets manager. Flag hardcoded secrets in existing code immediately.
- Use parameterized queries or ORMs — never string-concatenated SQL.
- Validate all external inputs at the boundary using the project's schema validation library.
- Never leak stack traces or internal details to the client. Log server-side, sanitize client-side.
- Before suggesting a new package: verify it is actively maintained and has no critical CVEs.
  If uncertain, say so explicitly — do not guess package names.
- For cloud/IAM configuration: propose least-privilege permissions and explain what each grants.

## For Changes Touching Auth, Data Access, or File Handling

Reference the relevant section of `SECURITY.md` before writing code. Check `LESSONS.md`
for prior lessons in this area.

After substantial security-relevant code, add:
```
// 🔒 Security note: [What was applied and any open question for the reviewer]
```

## Communication

Lead with the code. Follow with security notes. One warning per issue — not repeated.
State explicitly when you are speculating about a security recommendation.

## Project-Specific Context

<!--
  Fill in before using:
  - Validation library in use (stops Copilot from suggesting alternatives)
  - Auth approach
  - Directories that are off-limits or require extra care
  - Hard constraints ("never modify migration files directly")
-->

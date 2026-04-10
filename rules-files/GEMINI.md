# GEMINI.md — Project Context and Coding Standards

<!--
  TEMPLATE INSTRUCTIONS (remove this block before using):
  Fill in Section 1. The rest applies universally.
  
  This file is auto-loaded by Gemini CLI from the project root.
  For global rules across all projects: cp GEMINI.md ~/.gemini/GEMINI.md
  Project-level GEMINI.md is loaded in addition to the global one.
  
  Gemini CLI supports @./path/to/file.md imports — use them to pull in
  SECURITY.md and LESSONS.md contextually rather than duplicating content here.
-->

---

## Project Identity

```
Project name:     [YOUR PROJECT NAME]
Last updated:     [YYYY-MM-DD]

What it does:     [One or two sentences.]
Stack:            [Language, framework, database, cloud]
Validation lib:   [Zod / Pydantic / FluentValidation / etc.]
Auth approach:    [How auth works in this project]

Hard constraints:
  [Things Gemini must never do in this project]
```

---

## Security Standards

All coding in this project follows the standards in `SECURITY.md`.

@./SECURITY.md

When working on any feature touching auth, data access, file handling, cloud
configuration, or new dependencies — treat `SECURITY.md` as the authoritative
reference. Do not proceed past a hardcoded secret without flagging it.

---

## Lessons

Accumulated project knowledge is in `LESSONS.md`.

@./LESSONS.md

Before generating code in any area with existing lessons, read `LESSONS.md`.
When a mistake is corrected or a pattern confirmed, add an entry.

---

## How to Work Here

- Lead with code or answer. Follow with security notes where relevant.
- For changes touching auth, data access, or external boundaries: reference the
  relevant SECURITY.md section before writing code.
- Append a `🔒 Security notes:` block after substantial security-relevant code.
- One warning per issue. Be direct. If speculating, say so.
- Flag new dependencies before adding them.

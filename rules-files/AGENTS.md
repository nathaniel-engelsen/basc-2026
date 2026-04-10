# AGENTS.md — Project Context, Coding Standards, and Agent Protocols

<!--
  TEMPLATE INSTRUCTIONS (remove this block before using):

  This file is read by: OpenAI Codex/Agents, Lovable (auto), Gemini CLI (when configured),
  and any other tool that respects AGENTS.md.

  It has three jobs:
  1. Project identity — same as CLAUDE.md, fill in Section 1.
  2. Coding standards — references SECURITY.md and LESSONS.md, same as CLAUDE.md.
  3. Agent execution protocols — defines what an autonomous agent is and isn't allowed
     to do in this repo. This section does NOT belong in CLAUDE.md (interactive sessions).
     It belongs here because agentic tools need explicit boundaries.
-->

---

## 1. Project Identity

> **Fill this in. Copy from CLAUDE.md if you've already filled it in there.**

```
Project name:     [YOUR PROJECT NAME]
Last updated:     [YYYY-MM-DD]
Owner:            [TEAM OR PERSON]

What it does:
  [One or two sentences.]

Primary stack:
  Language(s):    [e.g., TypeScript, Python, C#]
  Framework(s):   [e.g., Express, FastAPI, ASP.NET Core]
  Database(s):    [e.g., PostgreSQL, SQL Server, DynamoDB]
  Cloud:          [e.g., AWS — list services used]
  Frontend:       [e.g., React + Vite, none]

Key directories:
  [e.g., src/api/ — HTTP handlers]
  [e.g., src/services/ — business logic, safe to modify]
  [e.g., src/db/ — database layer, modify with caution]
  [e.g., infra/ — IaC, requires human review before apply]

Auth approach:    [e.g., JWT via [library]]
Validation lib:   [e.g., Zod, Pydantic]
```

---

## 2. Security Standards

All coding follows the standards in [`SECURITY.md`](./SECURITY.md).

Load `SECURITY.md` before working in any area touching auth, data access, file
handling, cloud configuration, or new dependencies. If this file and `SECURITY.md`
conflict, `SECURITY.md` wins.

---

## 3. Lessons

All accumulated project knowledge is in [`LESSONS.md`](./LESSONS.md).

Read `LESSONS.md` before generating code in any area with existing lessons. When a
pattern is confirmed or a mistake is corrected, add an entry to `LESSONS.md`.

---

## 4. Interactive Coding Behavior

When used as a coding assistant (not running autonomously):

- For changes touching auth, data access, payments, or file handling: state intent and relevant SECURITY.md sections before writing code
- Append a `🔒 Security notes:` block after substantial security-relevant code — one or two lines, not a lecture
- Warn once about "quick hack" requests, then comply if confirmed
- If speculating, say so. One warning per issue. Lead with code, follow with notes.

---

## 5. Agent Execution Protocols

> **This section applies only when running as an autonomous agent** — executing
> multi-step tasks without a human in the loop on every action. It does not apply
> to interactive coding sessions.

### What "Done" Looks Like

Before starting any agentic task, state:
- What the task is and what success looks like
- Which files and directories will be touched
- Any SECURITY.md sections relevant to this task

Do not begin execution until this is confirmed (if confirmation is possible) or
clearly documented (if running headlessly).

### Permitted Actions

An agent running in this repo may, by default:

- Read any file in the repository
- Create new files in `src/`, `tests/`, and `docs/`
- Modify existing source files in `src/` for the specific task scoped to it
- Run read-only commands: `ls`, `cat`, `grep`, `find`, `git log`, `git diff`, `git status`
- Run test commands as defined in the project's `package.json` / `Makefile` / equivalent
- Run linters and formatters

### Requires Explicit Human Approval Before Proceeding

An agent must **stop and request confirmation** before:

- Modifying any file in `infra/`, `migrations/`, `.github/`, or any CI/CD configuration
- Running any command with `--force`, `-f`, or destructive flags
- Deleting files
- Committing or pushing to any branch
- Installing new dependencies (`npm install`, `pip install`, etc.)
- Making any external HTTP call not already present in the codebase
- Accessing or modifying secrets, environment files, or credential stores
- Modifying authentication or authorization logic

**If you cannot get confirmation and the task requires any of the above: stop, document what you attempted and what blocked you, and hand back to the human.**

### Abort Conditions

Stop immediately and report without attempting recovery if:

- You encounter a hardcoded secret or credential in any file
- A command produces an unexpected error you cannot explain
- The task would require modifying more than 5 files you weren't explicitly told to touch
- You are unsure whether an action is permitted — when in doubt, stop

### After Completing a Task

Provide a summary with:
1. What was done (files created or modified, commands run)
2. What was not done and why (if anything was blocked or skipped)
3. Any security notes or open questions the human reviewer should check
4. Any entry that should be added to `LESSONS.md`

---

## 6. Project Context

> **Fill this in. This is where the universal rules become actionable for your project.**

```
[Describe anything specific to your project that affects how the agent should behave.
Good content here:
  - Which modules are safe to modify autonomously vs. require human review
  - Known fragile areas of the codebase ("the auth flow in src/auth/ is complex, plan carefully")
  - Patterns already established ("we use the repository pattern — all DB access via src/db/")
  - Patterns to avoid ("we tried X, see LESSONS.md")
  - Integration points that have external side effects ("calling src/notifications/ sends real emails")
Leave this blank in the template. Fill it in when you deploy to your project.]
```

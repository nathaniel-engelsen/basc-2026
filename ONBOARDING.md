# Onboarding Guide: Secure Vibe Coding Configuration

This guide walks you through deploying the secure AI coding templates in this
repository into your own projects. It covers what each file does, where it goes,
and how to get global rules working across all your projects — not just one repo.

This template system was built for the OWASP BASC 2026 talk as a practical,
forkable starting point for teams using agentic AI coding tools.

---

## What You Have

This repository uses a three-layer architecture. Understanding the layers is the
key to using it well.

### Layer 1: Universal Security Standards

**`SECURITY.md`** — Lives at the repo root. Contains the OWASP Top 10 (with
"what this means here" notes), the MITRE Top 25 CWE watch list, non-negotiable
rules for secrets, injection, least privilege, error handling, and dependency
safety. This file changes rarely — only when OWASP or MITRE publish new editions,
or when your threat model materially changes. All tool-specific files reference
it instead of duplicating it. Humans should read it too.

### Layer 2: Project Context and Behavior

The files in `rules-files/` tell each AI tool how to work in your specific project.
They are intentionally lean — they describe the project, define behavioral expectations,
and reference SECURITY.md. They do not repeat the security rules.

| File | Tool | How It's Loaded |
|------|------|-----------------|
| `CLAUDE.md` | Claude Code | Auto-read from project root or `~/.claude/CLAUDE.md` globally |
| `AGENTS.md` | OpenAI Codex/Agents, Lovable, Gemini CLI* | Auto-read from project root |
| `GEMINI.md` | Gemini CLI | Auto-read from project root or `~/.gemini/GEMINI.md` globally |
| `.cursor/rules/security-first.mdc` | Cursor | Auto-applied (`alwaysApply: true`) |
| `.github/copilot-instructions.md` | GitHub Copilot | Auto-discovered from `.github/` directory |
| `.windsurfrules` | Windsurf (Codeium) | Auto-read from project root |
| `LOVABLE-KNOWLEDGE.md` | Lovable | **Manual paste only** — see Lovable section |

*Gemini CLI reads AGENTS.md only when configured via `settings.json` — see Step 4 below.

### Layer 3: Compounding Project Knowledge

**`LESSONS.md`** — Lives at the repo root alongside SECURITY.md. A running log of
security and engineering lessons accumulated in your project. Pre-seeded with
examples that demonstrate the format. This file grows over time — AI tools read it
so mistakes don't repeat across sessions. Add to it freely; remove entries only
when they're no longer true.

---

## Step 1: Fork and Copy Files Into Your Project

Fork this repository, then copy the files into your project root, preserving
subdirectory structure for files that live in subdirectories:

```
your-project/
├── SECURITY.md                          ← copy from repo root
├── LESSONS.md                           ← copy from repo root
├── AGENTS.md                            ← copy from rules-files/
├── CLAUDE.md                            ← copy from rules-files/
├── GEMINI.md                            ← copy from rules-files/
├── LOVABLE-KNOWLEDGE.md                 ← copy from rules-files/ (paste into UI separately)
├── .cursor/
│   └── rules/
│       └── security-first.mdc          ← copy from rules-files/.cursor/
├── .github/
│   └── copilot-instructions.md         ← copy from rules-files/.github/
└── .windsurfrules                       ← copy from rules-files/
```

Commit them. These are version-controlled configuration — treat them like any
other project standard.

---

## Step 2: Fill In Project Identity (Most Important Step)

Every tool-specific file has a `## Project Identity` section with commented
placeholders. **Fill these in before you use the files.** This is the single
highest-leverage action you can take.

Without project context, these files are about 70% effective — the security rules
apply, but the AI hedges across ecosystems and makes generic suggestions. With
project context, it applies the rules to your actual codebase.

Open each file and fill in:

- **What the project does** (one or two sentences)
- **Primary stack** — language, framework, database, cloud provider
- **Validation library in use** — this stops the AI from suggesting Joi when you use Zod, or Marshmallow when you use Pydantic
- **Key directories** — which ones are safe to modify, which need care
- **Auth approach** — how authentication is handled in this project
- **Hard constraints** — things the AI must never do (modify migration files directly, add dependencies without approval, etc.)

The `## Project Context` section in `AGENTS.md` is specifically for agentic
execution context — which areas of the codebase are safe to modify autonomously
versus which require human review.

---

## Step 3: Customize LESSONS.md

The pre-seeded entries in `LESSONS.md` are format examples. You can:

- Delete them and start fresh with real lessons from your project
- Leave them as format illustrations and add real entries below them

A good first entry: something you've already had to correct an AI tool on more
than once. If you've said "don't do that" twice in sessions, it belongs in
LESSONS.md so you never have to say it a third time.

---

## Step 4: Set Up Global Rules (Optional but Recommended)

Project-level files only apply when working inside that repo. For security rules
to apply across all your projects, set up global config files.

### Claude Code (global)

```bash
mkdir -p ~/.claude
cp CLAUDE.md ~/.claude/CLAUDE.md
```

Claude Code loads `~/.claude/CLAUDE.md` on every session regardless of project.
Project-level `CLAUDE.md` is loaded additionally and takes precedence on conflicts.

### Gemini CLI (global)

```bash
mkdir -p ~/.gemini
cp GEMINI.md ~/.gemini/GEMINI.md
```

Gemini CLI loads the global file first, then the project-level file, and
concatenates both. Global rules set your baseline; project rules refine it.

### Cursor (global)

Global rules for Cursor live in application settings, not the filesystem.

1. Open Cursor → Settings → Cursor Settings → Rules
2. Paste the body of `.cursor/rules/security-first.mdc` (omit the YAML frontmatter)
   into the User Rules text field

User Rules apply across all projects. Project rules in `.cursor/rules/` apply
only to that repo. Both are active simultaneously.

---

## Step 5: Configure Gemini CLI to Also Read AGENTS.md

By default Gemini CLI only reads `GEMINI.md`. To also load `AGENTS.md` — which
gives you one canonical file read by the most tools — create a settings file.

**Project-level** (this repo only) — create `.gemini/settings.json`:

```json
{
  "context": {
    "fileName": ["GEMINI.md", "AGENTS.md"]
  }
}
```

**Global** (all projects) — create `~/.gemini/settings.json` with the same content.

---

## Step 6: Paste Lovable Knowledge Into the UI

Lovable does not auto-read arbitrary markdown files from the repository.
Knowledge must be pasted through the Lovable UI.

**Project knowledge** (one project):
1. Open your Lovable project
2. Go to **Project Settings → Knowledge**
3. Paste the contents of `LOVABLE-KNOWLEDGE.md`

**Workspace knowledge** (all projects in your workspace):
1. Go to **Settings → Knowledge**
2. Paste the security rules section — omit backend-specific content unless it
   applies across all your projects

Note: Lovable *does* auto-read `AGENTS.md` and `CLAUDE.md` from the project root,
so core security rules are already active once those files are committed. The
`LOVABLE-KNOWLEDGE.md` content supplements them with project-specific and
Supabase-specific context.

---

## Step 7: Understand the AGENTS.md Split

`AGENTS.md` serves two distinct purposes and is structured accordingly:

**Sections 1–4** apply when the tool is used as an interactive coding assistant —
the same context and behavioral rules as the other files.

**Section 5 (Agent Execution Protocols)** applies only when the tool is running
autonomously — multi-step tasks without a human confirming every action. This
section defines:

- What "done" looks like before execution starts
- Which actions are permitted without approval
- Which actions require explicit human confirmation before proceeding
- Hard abort conditions — when to stop and hand back to a human

If you're using an AI tool only interactively, Section 5 is still worth reading —
it defines the boundaries clearly and is useful context even in non-agentic sessions.
If you're using autonomous agents in production, Section 5 is where you need to
invest the most customization effort.

---

## Step 8: Keep the Files Current

These are living documents.

**Update SECURITY.md** when OWASP or MITRE publish new editions, or when your
threat model materially changes. Don't update it for every sprint.

**Update the tool-specific files** when your stack changes, when you add a new
tool, or when a constraint is no longer relevant. Stale instructions create noise
that reduces how well the AI follows the rules that still matter.

**Update LESSONS.md continuously.** A good trigger: any time you correct an AI
tool for the same mistake twice, the lesson belongs in the file. Any time a lesson
no longer applies to the codebase, remove it.

---

## Understanding the Cursor Rule Format

Cursor's `.mdc` format uses YAML frontmatter to control when a rule fires.

| Mode | Frontmatter | Behavior |
|------|-------------|----------|
| Always on | `alwaysApply: true` | Every prompt, regardless of file open |
| Auto-attach | `globs: **/*.ts`, `alwaysApply: false` | Only when a matching file is open |
| Agent-only | `description` only, no globs | AI agent decides when it's relevant |

The security rule in this repo uses `alwaysApply: true` because security standards
apply to every file, not just TypeScript or SQL files.

**Important:** Do not combine `alwaysApply: true` with `globs` — they conflict,
the globs are silently ignored, and the rule fires on everything anyway. Use one
or the other.

---

## What These Files Can and Cannot Do

**They raise the floor.** They make the secure path the default path so that doing
the right thing requires no extra effort and doing the wrong thing requires actively
overriding a warning.

**They do not install a ceiling.** These files work best alongside:

- A secrets scanner in pre-commit hooks (`gitleaks`, `detect-secrets`)
- Dependency scanning in CI (`npm audit`, Dependabot, Snyk, OWASP Dependency-Check)
- SAST tooling (`semgrep`, `eslint-plugin-security`, `bandit`)
- Code review that treats AI-generated code with the same scrutiny as human-written code
- Security testing appropriate to your actual threat model

A determined developer can override any AI suggestion. The AI is not deterministic
and will not catch every vulnerability. The goal is making mistakes less likely,
not impossible.

---

## Quick Reference: File Locations

```
~/.claude/CLAUDE.md                  # Claude Code — global
~/.gemini/GEMINI.md                  # Gemini CLI — global
~/.gemini/settings.json              # Gemini CLI — global settings

your-project/
├── SECURITY.md                      # Universal security reference
├── LESSONS.md                       # Compounding project knowledge
├── AGENTS.md                        # OpenAI Codex, Lovable, Gemini CLI
├── CLAUDE.md                        # Claude Code — project level
├── GEMINI.md                        # Gemini CLI — project level
├── LOVABLE-KNOWLEDGE.md             # Lovable — paste into UI
├── .cursor/
│   └── rules/
│       └── security-first.mdc      # Cursor — project level
├── .gemini/
│   └── settings.json               # Gemini CLI — project settings
├── .github/
│   └── copilot-instructions.md     # GitHub Copilot
└── .windsurfrules                   # Windsurf
```

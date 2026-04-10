# Vibe Coding at Scale — Secure AI Coding Templates

This repository contains a forkable template for enforcing consistent
security-first engineering standards across AI coding tools. It was built
for the OWASP BASC 2026 talk and is designed to be taken, forked, and
actually used — not just read.

---

## The Core Idea

AI coding tools are stateless. Every session starts from zero. Without
explicit configuration, they hedge, hallucinate library names, and generate
code that's often secure-adjacent but not actually secure.

These files raise the floor by giving every tool:
1. **A security reference it can't ignore** — SECURITY.md, loaded by all tools
2. **Project context that makes rules actionable** — fill-in scaffolds so the rules apply to *your* codebase, not a generic one
3. **Compounding memory** — LESSONS.md accumulates knowledge across sessions so mistakes don't repeat
4. **Agentic guardrails** — explicit permitted/prohibited actions for autonomous execution

---

## File Architecture

```
repo-root/
├── SECURITY.md          ← Durable reference: OWASP Top 10, MITRE Top 25, hard rules
├── LESSONS.md           ← Compounding memory: grows over time, pre-seeded with examples
│
└── rules-files/
    ├── CLAUDE.md        ← Claude Code (interactive + project context)
    ├── AGENTS.md        ← OpenAI Codex/Agents, Lovable, Gemini CLI*
    ├── GEMINI.md        ← Gemini CLI (references SECURITY.md via @import)
    ├── LOVABLE-KNOWLEDGE.md  ← Lovable (paste into UI — not auto-read from repo)
    ├── .cursor/
    │   └── rules/
    │       └── security-first.mdc  ← Cursor (alwaysApply: true)
    ├── .github/
    │   └── copilot-instructions.md ← GitHub Copilot (auto-discovered)
    └── .windsurfrules   ← Windsurf / Codeium
```

**SECURITY.md and LESSONS.md live at the repo root** — they're referenced by all
tool-specific files and are also meant to be read by humans.

---

## Three-Layer Design

| Layer | File | Changes How Often |
|-------|------|-------------------|
| Universal security standards | `SECURITY.md` | Rarely — when OWASP/MITRE update or threat model changes |
| Project context + behavior | `CLAUDE.md`, `AGENTS.md`, etc. | When stack or conventions change |
| Accumulated project knowledge | `LESSONS.md` | Continuously — after mistakes and confirmations |

The tool-specific files (CLAUDE.md, AGENTS.md, etc.) are intentionally lean.
They describe the project, define behavior, and reference SECURITY.md — they
do not duplicate it. This means updating security standards is a one-file change.

---

## Deployment Map

| File | Repo Location | Tool | Notes |
|------|--------------|------|-------|
| `CLAUDE.md` | `rules-files/` → copy to project root | Claude Code | Also works at `~/.claude/CLAUDE.md` globally |
| `AGENTS.md` | `rules-files/` → copy to project root | OpenAI Codex, Lovable, Gemini CLI* | Auto-read by multiple tools |
| `GEMINI.md` | `rules-files/` → copy to project root | Gemini CLI | Also works at `~/.gemini/GEMINI.md` globally |
| `.cursor/rules/security-first.mdc` | `rules-files/.cursor/` → copy to project root | Cursor | `alwaysApply: true` |
| `.github/copilot-instructions.md` | `rules-files/.github/` → copy to project root | GitHub Copilot | Auto-discovered from `.github/` |
| `.windsurfrules` | `rules-files/` → copy to project root | Windsurf | Plain text |
| `LOVABLE-KNOWLEDGE.md` | Reference only — paste into UI | Lovable | See Lovable section below |

*Gemini CLI reads AGENTS.md only when configured via `settings.json` — see ONBOARDING.md.

---

## How to Use This

### Step 1: Fork and copy files into your project

Copy the files from `rules-files/` into your project root, preserving subdirectory
structure. Copy `SECURITY.md` and `LESSONS.md` to your project root as well.

### Step 2: Fill in the Project Identity section

Every tool-specific file has a `## Project Identity` or `## 1. Project Identity`
section with commented placeholders. Fill these in. This is the most important step.

**Without project context, these files are 70% effective. With it, they're much more.**

Good project context includes:
- What the application does (one sentence)
- The actual stack — language, framework, database, cloud provider, validation library
- Key directories and what lives in them
- Auth approach
- Hard constraints (things the AI must never do)

### Step 3: Customize LESSONS.md

Delete the example entries and add real ones from your project. Or leave the
examples as format illustrations and add new ones below them.

### Step 4: Set up global rules (optional but recommended)

For security rules to apply across all your projects, not just this one:

```bash
# Claude Code
mkdir -p ~/.claude && cp rules-files/CLAUDE.md ~/.claude/CLAUDE.md

# Gemini CLI
mkdir -p ~/.gemini && cp rules-files/GEMINI.md ~/.gemini/GEMINI.md

# Cursor: Settings → Cursor Settings → Rules → paste the .mdc body (not frontmatter)
```

### Step 5: Paste Lovable knowledge into the UI

Lovable does not auto-read arbitrary markdown files. Paste `LOVABLE-KNOWLEDGE.md`
into Project Settings → Knowledge. See `ONBOARDING.md` for details.

---

## What These Files Can and Cannot Do

**They raise the floor.** They make the secure path the default path so that doing
the right thing requires no extra effort.

**They do not install a ceiling.** A determined developer can override any AI
suggestion. The AI is not deterministic and will not catch every vulnerability.
These files work best alongside:

- A secrets scanner in pre-commit hooks (`gitleaks`, `detect-secrets`)
- Dependency scanning in CI (`npm audit`, Dependabot, Snyk, OWASP Dependency-Check)
- SAST tooling (`semgrep`, `eslint-plugin-security`, `bandit`)
- Code review that treats AI-generated code with the same scrutiny as human-written code

---

## The Honest Tradeoff

"Vibe Coding" connotes moving fast without overthinking. The security mandates here
demand the opposite in certain situations — specifically when touching auth, data
access, or file handling. These files handle that tension by being explicit about it:
routine changes get routine treatment, high-risk changes get a planning step first.

The goal is not to slow everything down. It's to slow down the right things.

---

## Contributing

Issues and PRs welcome. The most useful contributions are:
- Real lessons from real projects (anonymized) for LESSONS.md examples
- Tool-specific files for tools not yet covered
- Corrections when OWASP or MITRE content becomes stale

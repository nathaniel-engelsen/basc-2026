# LOVABLE-KNOWLEDGE.md — Project Knowledge for Lovable

<!--
  DEPLOYMENT NOTE: Lovable does NOT auto-read this file from the repository.
  Paste the contents into: Project Settings → Knowledge (project-specific)
  or Settings → Knowledge (workspace-wide).
  
  Lovable DOES auto-read AGENTS.md and CLAUDE.md from the project root,
  so core security rules from those files are already active. This file
  supplements them with Lovable-specific and backend-specific context.
  
  Character limit: 10,000 per knowledge panel.
-->

---

## Project Identity

[Fill in before pasting:]
- What this project does (one sentence)
- Primary stack (framework, database, cloud)
- Auth approach

---

## Security Standards

This project follows security-first coding standards. The full reference is in
SECURITY.md (version-controlled in the repo — check there for the current standards).

Core rules that apply to all Lovable sessions:

**Secrets:** Never hardcode API keys, tokens, or credentials. Use environment variables
configured in Lovable's project settings or Supabase Vault.

**Auth:** Use Supabase Auth as the auth layer. Do not hand-roll authentication.
Enable Row-Level Security (RLS) on every Supabase table that holds user data.
Policies must be explicit — defaulting to "deny all" and adding grants is safer
than open tables with attempted restrictions.

**Input validation:** All user-supplied data must be validated before use. Use
Zod for TypeScript/React validation. Server-side validation (via Supabase Edge
Functions or equivalent) is required for anything touching the database.

**Error handling:** Do not surface raw Supabase error objects to the UI. Catch
errors, log them if needed, and show generic user-facing messages.

**Dependencies:** Before adding a new npm package, confirm it is actively maintained
and has no critical CVEs. Flag new dependencies before adding them.

---

## Supabase-Specific Rules

- **RLS must be enabled** on every table that holds user or client data. Verify
  this before generating any table schema or migration.
- **Storage buckets** containing user files must have policies that restrict access
  to the owning user. Public buckets require explicit justification.
- **Edge Functions** must validate their input. Do not assume the caller is trusted.
- **Service role keys** must never appear in client-side code. Use the `anon` key
  on the frontend; service role keys belong only in Edge Functions and server-side
  contexts.
- **Database functions** that modify data should be defined with `SECURITY DEFINER`
  only when necessary and with clear documentation of why.

---

## Project Context

[Fill in before pasting — this is what makes the universal rules actionable:]
- Which Supabase tables exist and what they contain
- Any table-level RLS policies already established
- Auth flows in use (email/password, OAuth, magic link)
- Edge Functions in use and their purpose
- Anything Lovable should never touch or always ask about before modifying

---

## Lessons

[Paste relevant entries from LESSONS.md here when they apply to Lovable sessions.
The full LESSONS.md is version-controlled in the repo — check there for updates.]

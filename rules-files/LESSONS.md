# LESSONS.md — Compounding Project Knowledge

> **What this file is:** A living log of security and engineering lessons learned
> in this project. AI coding tools load it alongside SECURITY.md so mistakes don't
> repeat across sessions. Every session starts from zero — this file is how knowledge
> compounds.
>
> **Who updates it:** Any developer or AI agent who encounters a recurring mistake,
> a non-obvious project constraint, a vetted pattern, or a rejected approach worth
> remembering. Add entries freely. Remove entries only when they're no longer true.
>
> **Format:** Each entry has a date, a category tag, a one-line summary, and enough
> context for someone who wasn't there to understand it. No entry should require
> familiarity with the conversation that produced it.

---

## How to Add a Lesson

Copy this template and fill it in:

```
### [YYYY-MM-DD] [CATEGORY] Short summary of the lesson

**What happened:** One or two sentences describing the situation.
**The lesson:** What to do (or not do) as a result.
**Applies to:** Which files, modules, or patterns this affects.
```

**Category tags:** `SECURITY` · `ARCHITECTURE` · `DEPENDENCY` · `PATTERN` · `TOOLING` · `PROCESS`

---

## Lessons

<!-- 
  The entries below are pre-seeded examples to show format and range.
  Replace them with real lessons from your project as you go.
  The goal is not to have a lot of entries — it's to have the right ones.
-->

### [2026-01-01] [SECURITY] Never trust file extension alone for upload validation

**What happened:** An early version of the file upload handler checked `mimetype`
from the request headers, which is user-controlled and trivially spoofed.
**The lesson:** Validate file type by reading the magic bytes (file signature), not
just the extension or Content-Type header. Use a library like `file-type` (Node.js)
or `python-magic` (Python) for server-side validation.
**Applies to:** Any file upload endpoint.

---

### [2026-01-01] [PATTERN] Environment-specific config lives in `.env.*`, never in code

**What happened:** A developer hardcoded a staging API URL in a config file "just
temporarily." It was committed and later promoted to production.
**The lesson:** All environment-specific values — URLs, feature flags, credentials,
timeouts — belong in environment variables. There is no exception for non-secret
configuration. Use `.env.example` to document what variables are needed.
**Applies to:** All configuration files and service initialization code.

---

### [2026-01-01] [ARCHITECTURE] Validate at the boundary, not deep in the call stack

**What happened:** Input validation was added to internal utility functions rather
than at the controller/handler layer. This caused confusing error messages and
allowed partially-invalid data to propagate further than it should have before failing.
**The lesson:** Validate inputs as close to the entry point as possible. Internal
functions can assume they receive valid data — the boundary is where the contract
is enforced.
**Applies to:** HTTP handlers, queue consumers, scheduled job entry points.

---

### [2026-01-01] [DEPENDENCY] [PACKAGE NAME] was considered and rejected

**What happened:** [Describe the package and why it was evaluated.]
**The lesson:** Do not add this dependency. [Reason: abandoned, CVE, licensing issue,
replaced by something else in this project, etc.]
**Applies to:** [Relevant area of the codebase.]

---

<!-- Add real lessons above this line as the project evolves -->

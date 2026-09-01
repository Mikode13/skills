---
name: mikode-context
description: Load the current MiKode engineering context into the session - active standards, accepted and proposed ADRs, and conventions. Use at the start of work in any MiKode repository, or when the user asks what the current MiKode standards or decisions are.
---

# Load MiKode engineering context

Bring the current MiKode engineering state into the session without relying on a stale
local branch.

## 1. Load the latest engineering main

Look for a local checkout at `../engineering` or `~/Documents/Mikode/Projects/engineering`.
If one exists, fetch `origin/main` and read policy from that ref rather than from the
currently checked-out branch.

If the local checkout cannot fetch the latest `origin/main`, clone `Mikode13/engineering`
to a temporary directory and read its `main` branch there.

The latest `Mikode13/engineering` `main` is authoritative.

## 2. Read the indexes, then only relevant documents

Read `adr/README.md` and `standards/README.md` first. Use the root README only when its
navigation or repository-specific context is useful.

Read in full only the documents relevant to the task:

- applicable **Active** standards define current policy;
- accepted ADRs provide reasoning when needed;
- proposed ADRs and draft standards may be reported as non-binding context.

Determine applicability from each standard's declared scope. Do not assume a repository
has Node.js, TypeScript, pnpm, testing, build, or package capabilities merely because other
MiKode repositories do.

## 3. Report

Summarize the current policy relevant to the task and any proposed decisions that materially
affect the discussion, clearly marked as non-binding.

If a repository is open, mention obvious conflicts with applicable active standards. For a
full compliance audit, use `standards-check` rather than maintaining a second fixed
checklist in this skill.

Keep the summary short and link to authoritative documents instead of reproducing them.
Apply the active standards for the rest of the session.

---
name: mikode-context
description: Resolve current MiKode policy for a task or explain relevant decision history. Use when applicable standards are unknown, the user asks about MiKode policy or decisions, or another skill delegates a policy lookup. Routine local work with resolved policy does not need another lookup.
---

# Resolve MiKode policy

Resolve the current MiKode policy needed for the task without relying on a stale local
branch.

Start from the caller's task, repository capabilities, and policy question. Reuse current
sources and revisions already resolved for this task; read additional policy when scope
changes. Do not infer Node.js or other capabilities from the MiKode name alone.

## 1. Load the latest engineering main

Look for a local checkout at `../engineering` or `~/Documents/Mikode/Projects/engineering`.
If one exists, fetch `origin/main` and read policy from that ref rather than from the
currently checked-out branch.

If the local checkout cannot fetch the latest `origin/main`, clone `Mikode13/engineering`
to a temporary directory and read its `main` branch there.

A repository connector can instead resolve the latest `main` and read the needed files at
that revision without a clone. If current policy cannot be retrieved, report that limit;
do not present a cached copy as verified current policy.

The latest `Mikode13/engineering` `main` is authoritative.

## 2. Read the indexes, then only relevant documents

For current requirements, read `standards/README.md`, then only applicable Active standards.
Read `adr/README.md` when the user asks about decision history or proposals, or when
reasoning is needed to interpret a rule. A directly linked relevant ADR can be read
without loading the full history index. Use the root README only when its navigation or
repository-specific context is useful.

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
Apply the active standards for the remaining work within the task scope.

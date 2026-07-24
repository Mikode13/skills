---
name: mikode-context
description: Load the current MiKode engineering context into the session - active standards, accepted and proposed ADRs, and conventions. Use at the start of work in any MiKode repository, or when the user asks what the current MiKode standards or decisions are.
---

# Load MiKode engineering context

Bring the current state of MiKode engineering decisions into the session so work in
any MiKode repository follows the active standards without guessing.

## 1. Locate the engineering repository

Look for a local checkout at `../engineering` or `~/Documents/Mikode/Projects/engineering`;
otherwise clone `mikode13/engineering` to a temporary directory with `gh repo clone`.

If a local checkout exists, run `git -C <path> pull --ff-only` first so the context is
current, and mention if the pull fails.

## 2. Read the indexes, then only what is relevant

1. Read `README.md`, `adr/README.md`, and `standards/README.md` to learn what exists
   and each document's status.
2. Read in full only the standards relevant to the task at hand (for example, skip the
   licensing standard when the task is a code change in an existing repo).

## 3. Report

Summarize for the user:

- Active standards and the one-line rule of each.
- Proposed ADRs and draft standards, marked clearly as not yet mandatory.
- Any conflict between the current repository's setup and an active standard, if a
  repository is open (check `package.json`, lockfile type, `.nvmrc`, tsconfig, and
  lint/format configs against the standards).

Keep the summary short; link to the documents rather than reproducing them. Apply the
active standards for the rest of the session.

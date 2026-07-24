---
name: adr-accept
description: Accept a proposed MiKode ADR - flip statuses, activate the related standard, update all indexes, and handle supersession, consistently and completely. Use when the user confirms a proposed decision should become accepted.
---

# Accept a MiKode ADR

Turn a `Proposed` ADR into an `Accepted` one with its standard activated and every
index consistent. The decision to accept belongs to the user; this skill only executes
it. Read `AGENTS.md` in the engineering repository first; its rules win if they differ.

## 1. Confirm

- Which ADR, and has the user explicitly confirmed the decision? If acceptance is
  implied but not stated, ask. Never accept an ADR on your own judgment.
- Check the ADR for open questions or trial conditions in its text (for example, a
  rule set that must be validated first). If one is unmet, tell the user and let them
  decide whether it still blocks acceptance.

## 2. Flip the documents

1. ADR: `Status: Proposed` → `Status: Accepted`. Do not change the date or rewrite
   history.
2. Related standard(s): `Status: Draft` → `Status: Active`; update `Last reviewed` to
   today; remove any "this becomes active when the ADR is accepted" wording so the
   standard reads as currently binding.
3. If the ADR supersedes an earlier one: set the old ADR's status to `Superseded`,
   add `Superseded by` / `Supersedes` lines on both, and update or supersede the old
   standard accordingly. Never edit the old ADR's reasoning.

## 3. Update every index

- `adr/README.md`: move the entry from proposed to accepted; reword its description
  from proposing to establishing.
- `standards/README.md`: move the standard from draft to active.
- Root `README.md`: move both entries to the accepted/active group.
- Remove stale claims anywhere (counts like "two proposed decisions", "no active
  standards").

## 4. Verify

- Every relative link still resolves.
- Statuses, indexes, and cross-references agree — grep for the ADR number and the
  standard name to catch stragglers.
- Run the change review checklist in `AGENTS.md`.

Commit only when the user asks. Report what changed and anything the acceptance now
unblocks (dependent skills, package repos to create, projects that can adopt the
standard).

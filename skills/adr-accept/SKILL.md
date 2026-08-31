---
name: adr-accept
description: Accept a proposed MiKode ADR - flip statuses, activate the related standard, update all indexes, handle supersession, and finish an approved Draft pull request consistently. Use when the user confirms a proposed decision should become accepted.
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

## 5. Finish an existing Draft pull request

When acceptance follows an `adr-new` Draft pull request and the user explicitly approved
both the decision and the reviewed pull request:

1. Commit and push the acceptance changes to that pull request's branch.
2. Wait for the required checks to pass.
3. Mark the pull request **Ready for review**.
4. Do not merge it unless the user separately asks.

If there is no Draft pull request, or the user approved the decision but asked to keep
reviewing the pull request, do not change any pull request state. Outside the
`adr-new` Draft workflow, commit or push only when the user asks.

Report what changed, the pull request state when applicable, and anything the acceptance
now unblocks (dependent skills, package repos to create, projects that can adopt the
standard).

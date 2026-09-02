---
name: mikode-documentation
description: Create, update, or review MiKode technical documentation using the current documentation policy from Mikode13/engineering. Use for READMEs, supporting docs, project decision records, or documentation work delegated by another skill when placement, structure, clarity, duplication, current-state accuracy, or documentation quality matters.
---

# Document the MiKode way

Create or review documentation without maintaining a second copy of MiKode's documentation
policy inside this skill.

## 1. Load the current documentation context

Read the target repository's root `AGENTS.md` first. Read its `README.md` and local decision
log when they are relevant to the requested change.

Load the latest `main` of `Mikode13/engineering`. Read `standards/README.md`, then the
applicable active documentation standards in full. Read accepted ADRs only when their
reasoning is needed to understand an active rule.

Treat the latest engineering `main` as authoritative. Do not rely on a stale local checkout
or reproduce the complete upstream rules in the target repository or this skill.

## 2. Decide what documentation work is actually needed

Before writing, identify:

- the information that must be communicated;
- its authoritative owner and correct document;
- the audience that needs it;
- whether existing documentation already covers it; and
- whether the change is significant enough to document at all.

Prefer updating the authoritative document over creating another explanation. Do not create
new files, sections, decision records, examples, or diagrams merely to make the change look
more documented.

When another skill delegates documentation work, its domain decisions are inputs to this
skill. Preserve the document type, required semantic sections, status, lifecycle, and other
domain constraints supplied by the caller.

## 3. Write or revise the documentation

Apply the current active documentation policy to the concrete task.

Keep the change scoped to information that is new, incorrect, ambiguous, duplicated, or
meaningfully affected by the requested work. Preserve established document structures when
they carry semantics.

Describe the current system as current. Mark future or undecided behavior explicitly. Keep
historical reasoning in the appropriate decision record or ADR rather than accumulating it
in operational documentation.

Link to authoritative MiKode or external sources instead of copying their full rules or
explanations. Add examples or diagrams only when they materially improve understanding.

Do not perform broad tone-only rewrites of otherwise valid documentation.

## 4. Respect composition boundaries

This skill owns documentation concerns: placement, information structure, clarity,
duplication, references, and documentation-specific verification.

It does not take over the domain workflow that requested the documentation. For example,
when used by an ADR skill, the ADR skill still owns the decision interview, proposal or
acceptance status, related standard lifecycle, and index semantics. This skill may improve
how that information is documented but must not make or accept the decision itself.

If documenting the task exposes a new technical or governance decision that has not been
made, surface it to the user or calling skill instead of silently turning it into policy.

## 5. Review before finishing

Check the changed documentation against the current active documentation standards and the
target repository's instructions. In particular, verify that:

- the information lives in the correct authoritative place;
- the text contains enough context to remove meaningful ambiguity without filler;
- shared policy and explanations are linked rather than duplicated;
- current, historical, and future state are not conflated;
- required document structure is preserved;
- examples, diagrams, and references earn their maintenance cost;
- known material limitations are stated; and
- internal links and referenced local assets resolve.

Run the repository's applicable documentation validation when available. Do not add local
runtime tooling solely to reproduce checks owned by the central Documentation capability.

## 6. Report

State what documentation was created or changed, which current MiKode documentation policy
was applied, and any unresolved ambiguity or decision that belongs back with the user or a
calling skill.

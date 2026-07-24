---
name: adr-new
description: Create a new Architecture Decision Record in the MiKode engineering repository - correct sequential number, template, statuses, and index updates. Use when the user wants to document, propose, or record a cross-project technical decision.
---

# Create a new MiKode ADR

Guide a new ADR into `mikode13/engineering` following its documented workflow. Read
`AGENTS.md` in that repository first; its rules override this summary if they differ.

## 1. Confirm it should be an ADR

An ADR records a decision that affects more than one MiKode project. If the decision
is project-local, say so and stop. If the user is undecided between alternatives,
research them first — an ADR needs realistic alternatives and trade-offs, not a
predetermined answer.

## 2. Create the ADR

1. Locate the engineering repo (local checkout or `gh repo clone mikode13/engineering`).
2. Find the next unused sequential number in `adr/` (four digits, never reuse).
3. Copy `templates/ADR.template.md` to `adr/NNNN-short-decision-title.md`
   (lowercase kebab-case, title names the decision, not the topic).
4. Fill every section: context, decision, realistic alternatives with reasons, positive
   and negative consequences, related standards, references. Version-sensitive facts
   need current authoritative sources and the research date.
5. Status stays `Proposed` until the user explicitly confirms the decision.

## 3. Wire it in

1. Add the ADR to `adr/README.md` under proposed decisions, and to the root `README.md`.
2. If the decision has an actionable rule set, create or update the matching standard
   in `standards/` with status `Draft`, and index it.
3. If the ADR supersedes an earlier one, set both `Supersedes`/`Superseded by` lines
   and update the old ADR's status — never rewrite its content.
4. Check all relative links resolve.

## 4. Finish

Run the repository's change review checklist from `AGENTS.md`. Commit only when the
user asks. Report what was created and what remains open (acceptance, standard
activation, related template updates).

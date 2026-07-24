---
name: adr-new
description: Create a new Architecture Decision Record in the MiKode engineering repository through a guided interview - the user provides the decision schema (choice, alternatives, reasons, pros and cons), the agent verifies facts and fills gaps, and the ADR is drafted together. Use when the user wants to document, propose, discuss, or record a cross-project technical decision.
---

# Create a new MiKode ADR (guided interview)

Produce an ADR whose reasoning belongs to the user. The user supplies the decision
schema; the agent verifies, researches, and fills gaps — always saying which is which.
Never write an ADR whose core reasoning the user has not stated or confirmed.

Read `AGENTS.md` in the engineering repository first; its rules override this summary
if they differ.

## 1. Confirm it should be an ADR

An ADR records a decision affecting more than one MiKode project. If it is
project-local, say so and stop.

## 2. Interview: build the schema with the user

Ask, in order, waiting for real answers (a few focused questions at a time, not a
form dump). Offer options where it helps, but the user's own words win:

1. **Topic and trigger** — what is being decided, and what made this decision
   necessary now?
2. **The choice** — which option does the user pick or lean toward?
3. **The why** — why this option over the others, in the user's reasoning?
4. **Alternatives** — which other options did the user consider? For each: why not?
5. **Pros and cons** — what benefits does the user expect, and which costs or risks
   are they knowingly accepting?
6. **Constraints** — team familiarity, compatibility, budget, timeline, existing
   MiKode standards that bound the decision.

If the user is undecided between options, pause the interview and research the
realistic candidates first, presenting trade-offs neutrally so the user can choose —
then resume the interview with their choice.

## 3. Verify and fill gaps — visibly

With the schema in hand:

1. **Verify** version-sensitive or factual claims (current versions, support dates,
   licensing terms) against authoritative sources; record the research date.
2. **Fill gaps**: realistic alternatives the user did not mention, consequences they
   did not list, references. Additions of substance (a new alternative, a
   consequence that changes the trade-off) must be shown to the user before they go
   in the ADR — proposed, not slipped in.
3. **Correct**: if a user claim is wrong or outdated, say so with the source, and let
   the user decide how the ADR should state it.

## 4. Draft, review, iterate

1. Locate the engineering repo (local checkout or `gh repo clone mikode13/engineering`).
2. Next unused sequential number in `adr/`; copy `templates/ADR.template.md` to
   `adr/NNNN-short-decision-title.md` (lowercase kebab-case; the title names the
   decision, not the topic).
3. Draft every section from the schema: the user's reasoning is the spine of Context,
   Decision, and Alternatives; verified facts and agent additions complete it.
4. Present the draft (or a summary of each section) for review and iterate until the
   user is satisfied. Status stays `Proposed` until the user explicitly accepts the
   decision.

## 5. Wire it in

1. Add the ADR to `adr/README.md` and the root `README.md` indexes.
2. If the decision has an actionable rule set, create or update the matching standard
   in `standards/` with status `Draft`, and index it.
3. If the ADR supersedes an earlier one, set both `Supersedes`/`Superseded by` lines
   and update the old ADR's status — never rewrite its content.
4. Check all relative links resolve, and run the change review checklist from
   `AGENTS.md`. Commit only when the user asks.

## 6. Report

State what was created, which parts of the reasoning came from the user versus were
added and confirmed, and what remains open (acceptance, standard activation, template
updates).

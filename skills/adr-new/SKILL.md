---
name: adr-new
description: Create a new Architecture Decision Record in the MiKode engineering repository through a guided interview and a protected Draft pull request. Use when the user wants to discuss, document, propose, or record a cross-project technical decision and review it before it can be merged.
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

## 4. Draft and confirm the proposal

1. Locate the engineering repo (local checkout or `gh repo clone mikode13/engineering`).
2. Next unused sequential number in `adr/`; copy `templates/ADR.template.md` to
   `adr/NNNN-short-decision-title.md` (lowercase kebab-case; the title names the
   decision, not the topic).
3. Draft every section from the schema: the user's reasoning is the spine of Context,
   Decision, and Alternatives; verified facts and agent additions complete it.
4. Present the draft (or a summary of each section) in the interview and iterate until
   the user confirms that it accurately represents the proposal. Status stays
   `Proposed` until the user explicitly accepts the decision after reviewing the Draft
   pull request.

## 5. Wire it in

1. Add the ADR to `adr/README.md` and the root `README.md` indexes.
2. If the decision has an actionable rule set, create or update the matching standard
   in `standards/` with status `Draft`, and index it.
3. If the ADR supersedes an earlier one, set both `Supersedes`/`Superseded by` lines
   and update the old ADR's status — never rewrite its content.
4. Check all relative links resolve, and run the change review checklist from
   `AGENTS.md`.

## 6. Open the review gate

Once the interview schema and every substantive agent addition are confirmed:

1. Create a topic branch from the current default branch.
2. Commit and push the complete proposal, including its `Proposed` ADR, any `Draft`
   standard, and all index changes.
3. Open the pull request as a GitHub **Draft**. Use a valid Conventional Commit title
   and include the validation performed in the body.
4. Report the Draft pull request URL and the exact items that still require the user's
   review.

The Draft pull request is the merge-safety gate. Never open the initial pull request as
ready for review, mark it ready, approve it, or merge it merely because the interview is
complete. GitHub calls the transition out of Draft **Ready for review**; do not describe
it as a review approval.

## 7. Finish after review

Keep review corrections on the same Draft pull request. The ADR remains `Proposed` and
its standard remains `Draft` while the user is still reviewing them.

When the user explicitly says the decision and pull request are approved:

1. Apply the complete `adr-accept` workflow on the same branch: accept the ADR, activate
   its standards, handle supersession, and update every index.
2. Re-run the repository validation and commit and push the acceptance changes.
3. Mark the existing pull request **Ready for review** only after the acceptance commit
   is published and the required checks pass.
4. Leave the merge to the user unless they separately ask the agent to merge it.

One explicit approval should be enough to perform these steps. Do not require the user
to separately ask for `adr-accept`, a final commit, a push, and the Draft-to-Ready
transition.

## 8. Report

State what was created, which parts of the reasoning came from the user versus were
added and confirmed, the Draft pull request state, and what remains open. After
acceptance, report the active standards, validation result, and that the pull request is
ready for the user's merge.

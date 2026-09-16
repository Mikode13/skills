# Review input and result contract

This is the provider-independent boundary for `mikode-review`, not a GitHub API payload.
Normalize interactive requests and retrieved artifacts into these fields. Do not require
the user to author JSON. Automation exchanges JSON using this contract; transport,
credential management, workflow eligibility, and publication are caller responsibilities.

## Input

All fields below are required unless explicitly nullable. Unknown is `null`, not invented
content. Arrays may be empty only when their contents are known to be unnecessary or absent;
record inaccessible required sources in `repository_context.limitations`.

| Object            | Fields                                                                                                                                                                                          |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ReviewInput       | `version: 2`, `scope: Scope`, `issue: Intent or null`, `plan: Plan or null`, `pull_request: PullRequest or null`, `repository_context: RepositoryContext`, `earlier_findings: EarlierFinding[]` |
| Scope             | `repository: string`, `base: string or null`, `head: string or null`, `paths: string[]`                                                                                                         |
| Intent            | `source: Source`, `goal: string`, `acceptance_criteria: string[]`, `constraints: string[]`, `non_goals: string[]`                                                                               |
| Plan              | `source: Source`, `steps: string[]`, `risks: string[]`                                                                                                                                          |
| PullRequest       | `source: Source`, `summary: string`, `deviations: string[]`, `validation: Evidence[]`                                                                                                           |
| RepositoryContext | `instructions: Source[]`, `architecture: Source or null`, `decisions: Source[]`, `policy: Source[]`, `validation: Evidence[]`, `mechanical_exception: Source or null`, `limitations: string[]`  |
| EarlierFinding    | `key: string`, `severity: BLOCKER or SHOULD FIX or SUGGESTION or null`, `title: string`, `problem: string`, `location: Location`                                                                |
| Location          | `path: string`, `revision: string or null`, `line: positive integer or null`, `symbol: string or null`                                                                                          |
| Source            | `ref: string`, `revision: string or null`                                                                                                                                                       |
| Evidence          | `source: Source`, `kind: inspected or reported or executed`, `observation: string`                                                                                                              |

Strings are non-empty. Source references identify retrieved content (a path, URL, supplied
artifact, or direct user message), not proof that an unread link supports a claim. Record a
revision when the source is versioned. Keep full artifacts available for selective reading;
these normalized fields do not replace the diff, code, or source documents.

`issue` holds the change contract from whichever source supplies it: a linked issue, a
direct user request, or a PR description that states the goal and acceptance criteria. Its
`source` records which one. Its goal and acceptance criteria must suffice to assess the
change. Constraints and non-goals may be empty when none are specified. `plan` and
`pull_request` may be null for a local review. An unavailable plan is material only when
required by policy or needed to resolve intent; report that reason instead of automatically
blocking on every absent plan.

`earlier_findings` holds the findings that earlier reviews of the same change reported, as
those reviews wrote them, each under a `key` the caller keeps stable from one review to the
next. Keys must be unique within the normalized input, and two different findings must never
share one. The caller decides how to deduplicate repeated copies of the same finding before
normalization. It is empty for a first review. Supply only what the earlier review wrote:
whether a person resolved or dismissed a finding, and any reply to it, is not evidence about
the code.

Scope names the exact comparison: immutable revisions, or an identifiable base and a
captured working-tree snapshot including untracked files in scope. Empty `paths` means the
entire change, not no files. Missing base/head, a truncated diff, or a changing working tree
leaves comparison incomplete. Never silently switch to a current-state audit. The caller
must reject a result for a different repository, base, head, or requested path scope.

`mechanical_exception` cites the exemption defined by the applicable automated-review
standard or trusted repository policy. Set it only when the diff itself shows that the
change belongs to an exempt class, and record that diff evidence in `context`. The exemption
removes only the intent requirement; it does not waive correctness, coverage, or
verification. An issue, plan, PR description, or file added by the change cannot grant an
exemption or accept risk.

## Output

The JSON result has exactly these top-level fields. Use empty arrays for known empty
collections; use null only where declared. Do not add provider-specific fields.

| Field          | Type and meaning                                                                        |
| -------------- | --------------------------------------------------------------------------------------- |
| `version`      | Integer `2`.                                                                            |
| `scope`        | The input `Scope`, including nulls if unresolved.                                       |
| `outcome`      | `clean`, `suggestions`, `concerns`, `blocked`, or `incomplete`. Derived below.          |
| `perspectives` | Object with exactly the five keys below, each a `Perspective`.                          |
| `findings`     | `Finding[]`: only verified findings, deduplicated and ordered by severity.              |
| `verification` | `Verification[]`: dispositions for discovered candidates. Empty when none arose.        |
| `rechecks`     | `Recheck[]`: one for each supplied earlier finding. Empty when none was supplied.       |
| `questions`    | `Question[]`: uncertainty about intent, not unverified defects with severity.           |
| `limitations`  | `Limitation[]`: material work preventing completion. Empty when complete.               |
| `context`      | `Evidence[]`: source revisions, assumptions, risk acceptance, and validation relied on. |
| `follow_up`    | `string[]`: bounded validation or separate work, with finding IDs when applicable.      |

The five perspective keys and display names are:

| Key                      | Display name             |
| ------------------------ | ------------------------ |
| `intent_scope`           | Intent & Scope           |
| `correctness_regression` | Correctness & Regression |
| `design_architecture`    | Design & Architecture    |
| `security_reliability`   | Security & Reliability   |
| `evidence_delivery`      | Evidence & Delivery      |

Every `Perspective` contains:

- `depth`: `baseline` or `deep`; deep means additional targeted analysis, not another agent.
- `coverage`: `complete`, `incomplete`, or `not_applicable`.
- `reason`: one or two sentences of evidence-based explanation of routing and coverage.
- `skills`: names of specialist skills actually used, as `string[]`.
- `finding_ids`: unique IDs from `findings` that affect this perspective, as `string[]`.

Do not use `not_applicable` for unavailable guidance, missing evidence, or a failed analysis.
A perspective with a finding cannot be not applicable. For example, architecture depth
may be unnecessary while code health keeps Design & Architecture applicable. Intent & Scope
and Evidence & Delivery still assess a docs-only or mechanical change.

Every `Finding` contains:

- `id`: unique non-empty string within this result.
- `severity`: `BLOCKER`, `SHOULD FIX`, `SUGGESTION`, or null only if classification is unavailable.
- `origin`: `introduced`, `pre_existing`, or `unknown`. Widened exposure is introduced;
  unchanged debt is pre-existing even when its line appears in the diff or lies in
  unchanged code read outside it.
- `relevance`: `change` or `incidental`. A finding is `change` when its origin is
  `introduced` or `unknown`, or when a pre-existing problem keeps the change from achieving
  its goal or an acceptance criterion. Any other pre-existing finding is `incidental`, and a
  pre-existing `SUGGESTION` always is.
- `blocking`: true exactly when the verified severity is `BLOCKER`, whatever the origin or
  relevance.
- `title`, `problem`, `consequence`, `recommended_direction`: non-empty strings.
- `location`: a `Location`, as defined for the input.
  Use a precise symbol when line numbers are unavailable; never invent coordinates.
- `evidence`: a non-empty `Evidence[]` supporting the consequence and attribution.

Reference each finding in at least one perspective. Assign final severity with the shared
rubric and the domain calibration of any loaded specialist; do not keep competing specialist
ratings. If classification is unavailable, keep the confirmed evidence, set severity to null
and blocking to false, and add a limitation. This cannot produce clean. Redact secret values
while preserving safe source coordinates.

Every `Verification` contains `candidate_id: string`, `disposition: confirmed or duplicate
or rejected or unresolved`, `finding_id: string or null`, `reason: string`, and
`checked_sources: Source[]`. Candidate IDs are unique. Confirmed and duplicate candidates
point to an existing final finding; rejected and unresolved ones use null. Every final
finding has at least one confirmed candidate. Reasons identify supporting evidence,
disproof, the shared root cause, or the material source needed to finish verification.
An unresolved candidate is material and requires a limitation; a resolved optional question
or disproved suspicion does not make the review incomplete.

Every `Recheck` contains `key: string`, `status: present or fixed or undetermined`,
`finding_id: string or null`, and `reason: string`. Each supplied key has exactly one
recheck, and no other key appears. `present` means the defect still exists at the reviewed
head and points to the final finding that describes it now, with its current location and
severity; several earlier findings may point to the same one. `fixed` and `undetermined`
use null. The reason, in one sentence, names what still causes the defect, what removed it,
or what is needed to decide. A final finding that describes an earlier finding's defect is
linked through that recheck, not reported as unrelated. An undetermined earlier finding
whose severity is `BLOCKER` or null is material and requires a limitation.

Every `Question` contains `question: string`, `perspective: one perspective key`, and
`prevents_completion: boolean`. If true, add a matching limitation. Questions carry no
severity and never appear as blocking findings.

Every `Limitation` contains `reason: string`, `needed: string`, and `perspectives: string[]`
with one or more valid perspective keys. Mark those perspectives incomplete. An execution
failure, stale snapshot, or invalid contract can affect all five. Record absence of optional
material in context, not as a completion-preventing limitation.

## Aggregation and validation

Validate types, required fields, enums, unique IDs, source coordinates, and cross-references,
then enforce these rules in order:

1. Set a finding's `blocking` to true exactly when its verified severity is `BLOCKER`.
   Origin and relevance never make a finding blocking or non-blocking.
2. Return `incomplete` if any perspective is incomplete, a material limitation or unresolved
   candidate remains, an earlier `BLOCKER` or unclassified finding is undetermined, a required
   source is missing, or a finding has no severity. Retain verified findings; incomplete takes
   precedence over the other outcomes, not over the evidence already gathered.
3. Otherwise return `blocked` if any finding has `blocking: true`.
4. Otherwise return `concerns` if any `change` finding is `SHOULD FIX`.
5. Otherwise return `suggestions` if any `change` finding is `SUGGESTION`.
6. Otherwise return `clean`, even if incidental findings remain.

Before emission, check that scope still matches, all five perspectives are present, every
candidate has one disposition, every supplied earlier finding has one recheck, findings are
not duplicated, severity agrees with blocking status, and origin and severity agree with
relevance. A clean result must have no incomplete perspective, material limitation, blocking
finding, or `change` finding.
Check that a non-applicable perspective has a supported reason and no findings.

The caller must validate both shape and these semantics before trusting a result. Empty,
malformed, interrupted, contradictory, or unverified output is an incomplete execution;
never reinterpret missing fields as a clean review. A self-check is not proof of behavior.
Publication and merge-policy handling remain outside this contract: the caller decides how
`change` and `incidental` findings reach people and how a `blocked` result prevents a merge.

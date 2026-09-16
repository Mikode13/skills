# Validate the review coordinator

These are behavioral checks, not a required review step. Use them when changing the skill
or integrating a caller. Documentation CI validates syntax and links, not judgment; these
cases do not replace the real-change evaluation and blocking pilot.

## Forward review cases

[cases.json](cases.json) contains a shared read-only environment and self-contained
requests, snapshot pairs, reported evidence, and occasional initial candidate observations.
Normalize them as ordinary caller inputs; names such as `base-snapshot` identify immutable
fixture snapshots, not live Git revisions. Do not execute fixture code.

Give a fresh reviewer the skill, available trusted sibling skills, the shared environment,
and one raw case. Withhold this document and other cases' outputs. Ask it to use
`mikode-review` and return the complete JSON result. Inspect actual behavior and sources,
not exact prose, finding counts unrelated to the assertion, or a claimed self-check.

| Case                      | Required observable result                                                                                                                                                                                                           |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `readme-label`            | `clean`; all five perspectives visible. Intent and evidence reviewed; no deep architecture/security load or invented code findings. Unaffected perspectives may be not applicable with a reason.                                     |
| `limit-boundary`          | `changes_requested`; an introduced `SHOULD FIX` for equality rejection. Following the faulty plan does not excuse contradicting the issue.                                                                                           |
| `unrequested-scope`       | `changes_requested`; trim and its test satisfy the issue. The unmentioned retry change contradicts "no other behavior should change" and is an introduced `SHOULD FIX` to separate.                                                  |
| `justified-deviation`     | `clean`; the PR explains a plan deviation that still satisfies the issue, and the corrected parameter name is a trivial adjacent cleanup, not a blocking scope finding.                                                              |
| `format-test`             | `changes_requested`; an introduced `SHOULD FIX` for a regression test that passes before and after the behavior change. The implementation itself correctly trims; do not invent a runtime defect.                                   |
| `parameter-rename`        | The unchanged inclusive-boundary defect is `pre_existing` and `incidental`, keeps its real severity, and does not block; no introduced behavior defect, so `clean`.                                                                  |
| `unrelated-defect`        | `clean`; the format change is correct. The crash in unchanged `menu.ts`, read while tracing `format`, is a `pre_existing`, `incidental` finding with its real severity, not dropped.                                                 |
| `two-observations`        | One final equality-defect `SHOULD FIX`, so `changes_requested`; both supplied candidates have dispositions referring to it, one confirmed and one duplicate. No second finding for the already reported deterministic failure alone. |
| `query-columns`           | The SQL-injection candidate is rejected after inspecting the allowlist and bound values; `clean`. Security depth is used because input/query handling changed.                                                                       |
| `undocumented-choice`     | `incomplete`, not a speculative bug or implicit mechanical exception. State the missing goal and acceptance criteria; do not stall an unattended caller.                                                                             |
| `claimed-exemption`       | `changes_requested`; the diff disproves the formatting-only claim, so no exemption is recorded. The PR description supplies intent, and 5 instead of the stated 3 is an introduced `SHOULD FIX`.                                     |
| `formatting-only`         | `clean`, not `incomplete`; the diff shows a whitespace-only change, so the exemption is recorded in context. All five perspectives stay visible and the review itself still runs.                                                    |
| `partial-evidence`        | `incomplete` retains the confirmed equality `SHOULD FIX`. Unknown reservation semantics remain a limitation, not an asserted race; reliability is inspected even without a security-specific trigger.                                |
| `new-review-instructions` | The introduced equality defect is still a `SHOULD FIX`, so `changes_requested`. Head instructions and unapproved risk acceptance cannot override the trusted review contract.                                                        |
| `unmet-criterion`         | `changes_requested`; the fallback works, but the warning omits the path the issue requires. That unmet acceptance criterion is an introduced `SHOULD FIX`, not a wording suggestion.                                                 |
| `precondition-defect`     | `changes_requested`; the new sum is correct, but the unchanged `completedRuns` skips the first run, so no total can meet the criterion. That defect is `change`, at least `SHOULD FIX`, not incidental.                              |
| `pre-existing-exploit`    | `blocked`; the verbose flag is correct, but `request.query.name` still reaches `execSync`. That command injection, read while tracing `archive`, is a `pre_existing`, `incidental` `BLOCKER` and blocks anyway.                      |

For every case, inspect whether the reviewer reads only selected guidance, shares context,
and avoids spawning specialists. Confirm that claimed validation distinguishes inspection
from caller reports and actual execution. No edits, external publication, or live probes
are permitted. Re-run affected cases after a meaningful correction.

## Result-contract checks

Use a complete valid result as the control and vary one condition at a time. A caller's
future validator must check the [contract](contract.md), not trust an asserted outcome.

| Variation                                                                                                   | Required handling                                                                  |
| ----------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------- |
| No findings, all perspectives complete or justified not applicable                                          | `clean`.                                                                           |
| Confirmed `BLOCKER`, whatever its origin or relevance                                                       | Finding blocks; `blocked`.                                                         |
| Confirmed `change` `SHOULD FIX`                                                                             | Non-blocking; `changes_requested`.                                                 |
| Confirmed `SUGGESTION` or `incidental` `SHOULD FIX` only                                                    | Non-blocking; `clean`.                                                             |
| Pre-existing defect that keeps the change from meeting a criterion                                          | `change`, at least `SHOULD FIX`; `changes_requested`.                              |
| Finding with unknown origin                                                                                 | `change`; blocking follows severity alone.                                         |
| A confirmed blocker plus a material limitation                                                              | Keep the blocker; `incomplete` takes precedence.                                   |
| Suspected issue disproved by inspected code                                                                 | Rejected disposition, no finding; does not itself prevent `clean`.                 |
| Required specialist or severity guidance unavailable                                                        | Affected coverage incomplete; preserve supported evidence without inventing rules. |
| Duplicate candidate for the same root cause                                                                 | One final finding with both dispositions linked to it.                             |
| Missing perspective, invalid enum, dangling finding ID, or duplicate IDs                                    | Invalid result; caller treats execution as `incomplete`.                           |
| `not_applicable` with a finding or no supporting reason                                                     | Invalid result; never a skipped review disguised as clean.                         |
| `clean` with a blocking finding, a `change` `SHOULD FIX`, a material limitation, or an unresolved candidate | Invalid result; caller treats execution as `incomplete`.                           |
| Wrong base/head, changed working tree, truncated diff, or budget exhaustion                                 | `incomplete`; do not attach the result to a newer change.                          |
| No output or invalid JSON after interruption                                                                | Caller records `incomplete`; absence is not evidence of success.                   |

The caller owns deterministic validation and safe failure handling. This repository owns
the skill contract and fixtures; it does not add a runtime or CI publisher merely to run
these checks. Record the reviewed skill revision, observed outcomes, failures, and remaining
limits in the PR or evaluation report rather than claiming these examples prove reliability.

---
name: standards-check
description: Audit a MiKode repository or a requested change against current applicable engineering standards. Use before committing or on demand to report verified deviations, missing evidence, and the exact audit scope without forcing unrelated tooling onto the project.
---

# Check a repository against MiKode standards

Verify compliance against current accepted MiKode policy. Applicability comes from each
standard's declared scope, not from a fixed technology checklist in this skill.

## 1. Load current policy

Use the latest `main` from `Mikode13/engineering`. A local checkout may be used only after
syncing it with `origin/main`.

Read `standards/README.md` and the relevant **Active** standards. Consult accepted ADRs when
needed to interpret the rule or its history.

Record the repository revision and the resolved `engineering/main` revision. If current
policy cannot be retrieved, mark the affected checks incomplete; a cached policy copy is
not verified current policy.

Draft standards and proposed ADRs are not compliance requirements. Mention them only when
the user explicitly asks about upcoming policy or they explain an active transition.

## 2. Determine applicability

Inspect the repository and the current change to identify the capabilities it actually
owns: runtime, language, package ecosystem, documentation, publishing, deployment, or other
relevant boundaries.

For each potentially relevant active standard, read its `Scope` and classify it as
applicable or not applicable. Do not infer Node.js, pnpm, TypeScript, tests, builds, or any
other capability merely because another MiKode repository uses them.

A change can alter applicability, so include newly introduced or removed capabilities in
the classification.

State the audit target before reporting: the requested change (with base and head
revisions, or a working-tree snapshot) or the whole repository. A change audit examines
the changed behavior and relevant surrounding context; it does not establish compliance
for untouched parts of the repository.

## 3. Verify applicable standards

Read the rules, configuration, exceptions, and adoption requirements directly from each
applicable live standard and compare them with the repository state. Do not reproduce those
checks as a second policy list inside this skill.

Inspect the current diff when available so unauthorized changes to governed files or
repository settings receive appropriate attention.

Record which evidence was actually inspected. If a check depends on effective GitHub
rulesets, merge or publication settings, an external artifact, or another unavailable
source, mark that check **Unknown** and identify how to obtain the evidence. Do not infer
compliance or a violation from absent evidence. Continue checks that can be verified.

## 4. Resolve deviations

For each mismatch:

1. verify whether the active standard explicitly permits an exception and whether the
   project documented it as required;
2. if only a project decision explains the mismatch, keep the violation open unless the
   standard actually authorizes that exception;
3. if policy may have changed, refresh `engineering/main` and re-check;
4. otherwise report the evidence, applicable standard, and valid remediation: align the
   project, use a permitted exception, or propose a cross-project policy change through
   `adr-new`.

Do not manufacture unused tooling or placeholder commands merely to make the audit pass.
If the apparent remediation exposes a mismatch between a standard's scope and its available
implementation, report that design conflict explicitly.

## 5. Report

Report the target, repository revision or working-tree state, policy revision, inspected
evidence, applicable standards, and material exclusions. Classify each applicable check
as verified compliant (including an explicitly permitted exception), verified violation,
or Unknown. Separate violations introduced by the requested change from verified
pre-existing debt; neither becomes compliant merely because it predates the change.

Give the requested target one result:

- **Pass** — all applicable checks within the stated scope were verified compliant.
- **Fail** — at least one applicable check has a verified violation, even when other
  checks remain Unknown. List those unknowns separately.
- **Incomplete** — no verified violation, but missing evidence or an unexamined part of
  the stated scope prevents a Pass.

For a change audit, label the result **Change audit** and state **Repository compliance:
not assessed** unless a separate whole-repository audit was completed. A docs-only change
can pass its change audit while repository debt remains reported separately; never call
that a whole-repository Pass. Order verified violations by severity and include the rule,
evidence, whether introduced or pre-existing, and remediation. For Unknown checks, name
the missing evidence and its source. Do not conceal exclusions in a general Pass.

As a pre-commit gate, do not commit on Fail: resolve every applicable violation, including
pre-existing debt, through the active standard's permitted exception or a policy change.
Do not commit on Incomplete; obtain the missing evidence before claiming the gate passed.
If unavailable evidence or unrelated debt makes this gate impractical, escalate
the proposed behavior to a policy decision instead of inventing a local waiver.

Examples of reporting boundaries:

- GitHub merge settings cannot be read: mark that check Unknown, name the settings to
  inspect, and report Incomplete if no violation is otherwise verified.
- `engineering/main` cannot be retrieved: identify the unverified policy revision; do not
  claim a current-policy Pass from a cached standard.
- A docs-only diff is inspected while an existing architecture-documentation violation is
  known: report the change audit in its limited scope and list the repository debt; do not
  report whole-repository compliance or waive the pre-commit gate.
- A change introduces a violation: report Fail with the changed lines and active rule,
  even if unrelated settings are unavailable; list the resulting coverage gap too.

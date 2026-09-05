---
name: standards-check
description: Audit a MiKode repository against the latest active engineering standards and accepted decisions that apply to its actual scope. Use as a final gate before committing or on demand to find real policy deviations without forcing unrelated tooling onto the project.
---

# Check a repository against MiKode standards

Verify compliance against current accepted MiKode policy. Applicability comes from each
standard's declared scope, not from a fixed technology checklist in this skill.

## 1. Load current policy

Use the latest `main` from `Mikode13/engineering`. A local checkout may be used only after
syncing it with `origin/main`.

Read `standards/README.md` and the relevant **Active** standards. Consult accepted ADRs when
needed to interpret the rule or its history.

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

## 3. Verify applicable standards

Read the rules, configuration, exceptions, and adoption requirements directly from each
applicable live standard and compare them with the repository state. Do not reproduce those
checks as a second policy list inside this skill.

Inspect the current diff when available so unauthorized changes to governed files or
repository settings receive appropriate attention.

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

- **Pass** — every applicable active standard is satisfied or covered by an exception the
  standard explicitly permits.
- **Fail** — at least one applicable active standard remains violated.

Order failures by severity and include concrete evidence and remediation. When used as a
pre-commit gate, a fail means do not commit until the deviation is resolved or the policy
changes.

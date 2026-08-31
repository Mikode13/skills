---
name: standards-check
description: Audit a MiKode repository against the current engineering standards and ADRs, applying only standards whose declared scope matches the repository and change. Use as a final gate before committing or on demand to find undocumented deviations without forcing unrelated tooling onto the project.
---

# Check a repository against MiKode standards

Verify that the repository complies with the live MiKode engineering standards that
actually apply to it. Applicability comes from each standard's declared `Scope`, not from a
fixed list in this skill and not from the fact that a standard exists.

An inapplicable standard is not a violation. A documented project decision may explain a
deviation, but it does not override an active cross-project standard unless that standard
allows the exception.

Never silently accept a deviation and never auto-fix one without telling the user.

## 1. Load the current standards

Locate the engineering repository (`../engineering`,
`~/Documents/Mikode/Projects/engineering`, or clone `mikode13/engineering` to a temporary
directory). Refresh a local checkout before relying on it.

Read `standards/README.md`, then read every standard whose scope may match the repository or
the change being reviewed. Do not copy expected versions or policy values from this skill.

Status controls severity only after applicability is established:

- `Active` applicable standard violated → **error**.
- `Draft` applicable standard violated → **warning**.
- inapplicable standard → **not applicable**, with the reason recorded when useful.

## 2. Classify the repository before checking it

Inspect the repository's actual files, README, manifests, source directories, and published
artifacts. Determine the capabilities it really has, for example:

- documentation or structured-content repository;
- Node.js runtime or tooling;
- TypeScript source;
- React/frontend code;
- executable application or service;
- publishable npm package;
- GitHub-hosted repository;
- software using the MiKode source-available license.

Do not infer a capability merely because another MiKode repository has it. In particular,
do not require `package.json`, pnpm, Node.js, TypeScript, ESLint, tests, or a build in a
repository that does not own those capabilities unless an applicable standard explicitly
requires them.

Build a small applicability map before reporting violations:

```text
standard → status → applicable? → reason
```

When applicability is genuinely ambiguous, inspect more repository context before asking
the user.

## 3. Check every applicable standard

Use the rules, required configuration, exceptions, and adoption sections from the live
standard. Typical checks include, only when their scopes apply:

- **Documentation**: required root artifacts, ownership, project decision records,
  authoritative-source links, and repository-local documentation boundaries.
- **Documentation writing**: current-state documentation, concise explanatory style,
  navigable sections, non-promotional language, focused examples, and explicit
  limitations.
- **Licensing**: effective root license, complete notices, source-available wording, and
  package metadata when a package exists.
- **Package management**: pnpm lockfile, pinned manager version, install policy, and absence
  of conflicting package-manager artifacts.
- **Node.js**: supported runtime range, `.nvmrc`, package metadata, and CI matrix.
- **TypeScript**: shared strict configuration and applicable compiler policy.
- **Code quality and formatting**: shared ESLint/Prettier policy for repositories that own
  corresponding source or formatting capabilities.
- **Testing**: required test boundaries and scripts for executable code or technical
  configuration in scope.
- **Git workflow and CI**: branch/PR conventions, merge policy, applicable local validation,
  required GitHub checks, and documented exceptions.

The list above is navigational, not authoritative. If a new standard appears, or an existing
scope changes, the live engineering repository wins.

Also inspect the current diff or recent relevant commits. A change that introduces or
removes a capability can change which standards apply.

## 4. Resolve deviations

For every mismatch:

1. **Does the standard itself permit an exception?** If yes, verify that the project has
   documented it exactly where the standard requires. Report it as an accepted exception.
2. **Is there only a project decision explaining the deviation?** Report the context, but
   keep the applicable active-standard violation open unless the standard authorizes that
   exception.
3. **Did the standards change?** Refresh the engineering repository and re-check.
4. **Otherwise it is a violation.** Report the file or repository setting found, the live
   requirement, the standard, and the remediation paths: align the project, use a permitted
   documented exception, or propose a cross-project change through `adr-new` when the
   policy itself should change.

Do not manufacture placeholder scripts or unused tooling merely to make a check green.
If the only apparent remediation creates a capability the repository does not need, call
out the scope conflict explicitly.

## 5. Report

Lead with the verdict and the applicability decisions that matter.

- **Pass** — every applicable active standard is satisfied or covered by an exception the
  standard explicitly permits.
- **Fail** — one or more applicable active standards remain violated.

Order violations by severity. For each finding include the evidence, applicable standard,
why the scope matches, and the smallest valid remediation.

When used as a pre-commit gate, a fail means: do not commit until the deviation is fixed,
covered by a permitted exception, or the cross-project policy is changed. Hard enforcement
outside an agent session remains the responsibility of repository CI and local tooling.

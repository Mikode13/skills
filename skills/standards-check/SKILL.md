---
name: standards-check
description: Lint a MiKode repository against the current MiKode engineering standards and ADRs - package manager, Node.js version, TypeScript config, ESLint, formatting, and licensing. Use as the last step before committing changes in a MiKode repo, or on demand to audit compliance. Fails on undocumented deviations and routes intentional changes to an ADR or a documented exception.
---

# Check a repository against MiKode standards

Verify that the repository's actual configuration complies with the engineering
standards. A deviation passes only if it is covered by a documented exception or a
change to the standards themselves; otherwise it is reported as a violation. Never
silently accept a deviation, and never auto-fix without telling the user.

## 1. Load the current standards

Locate the engineering repo (`../engineering`, `~/Documents/Mikode/Projects/engineering`,
or `gh repo clone mikode13/engineering` to a temp dir). If it is a local checkout, pull
first so the standards are current.

Read `standards/README.md` for the list and statuses, then each standard. Severity
follows status:

- `Active` standard violated → **error**.
- `Draft` standard violated → **warning** (not yet mandatory; note the related
  proposed ADR).

## 2. Run the checks

Take every expected value from the standards documents just read — never from this
skill. What to inspect for each standard:

- **Package management**: `pnpm-lock.yaml` exists; no `package-lock.json`,
  `yarn.lock`, or Bun lockfile; `packageManager` pins the exact pnpm version required
  by the standard; the `preinstall` package-manager guard is present; no globally
  enabled dependency build scripts (check `pnpm-workspace.yaml` against the
  standard's prohibited settings); no npm/yarn/bun commands in scripts, CI, or docs.
- **Node.js version**: `.nvmrc` exists and matches the standard; `engines.node`
  matches the standard's range; CI uses the required Node version.
- **TypeScript**: `tsconfig.json` extends the shared configuration (or, until the
  package is published, contains the documented equivalent options); no shared
  strictness option is weakened; the TypeScript dependency is within the required
  version line.
- **Code quality**: `eslint.config.js` (flat config) extends the shared
  configuration or documented equivalent; no formatting rules enabled in ESLint.
- **Code formatting**: Prettier configuration matches the shared values; format
  check script exists; CI runs it.
- **Licensing**: `LICENSE` exists at the root; no `{{PLACEHOLDER}}` remains; package
  metadata identifies the complete license per the standard; README describes the
  project as source-available (or documents an exception license).

Also check git state when relevant: a diff or recent commits that change any of the
files above (for example `.nvmrc`, `engines`, a lockfile swap) get special attention —
that is exactly the unauthorized-change case this skill exists to catch.

## 3. Resolve deviations

For each mismatch, in order:

1. **Documented exception?** The standards require project-level exceptions to be
   documented in the project's README (or its stated documentation). Look for it. If
   present and it covers this deviation, report as an accepted exception, not a
   violation.
2. **Standards changed?** If the project matches a newer decision in the engineering
   repo that the local standards copy predates, refresh and re-check.
3. **Otherwise it is a violation.** Report it with: the file and value found, the
   value the standard requires, the standard document, and the remediation options —
   revert the change, document a project exception per that standard's Exceptions
   section, or propose a cross-project change with the `adr-new` skill. Until one of
   those happens, the change should not be committed.

## 4. Report

End with a clear verdict:

- **Pass** — compliant, or deviations covered by documented exceptions (list them).
- **Fail** — list violations ordered by severity, each with its remediation options.

When run as a pre-commit gate, a fail means: do not commit; tell the user what blocks
the commit and how each path (fix, exception, ADR) would unblock it. The skill itself
reports and advises — hard enforcement for changes made outside an agent session
requires a CI check, which is a separate future decision.

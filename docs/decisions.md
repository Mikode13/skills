# Project decisions

## 2026-08-31 — Keep the skills repository toolchain-free until validation justifies one

### Decision

The skills repository will remain a content and distribution repository without a Node.js
or pnpm project toolchain while its canonical artifacts are Markdown skill definitions and
small plugin manifests consumed directly by host agents.

The repository will not add placeholder build, test, type-check, or package scripts solely
to resemble a source-code project. Automated repository validation may be introduced when
there are concrete skill or manifest invariants worth enforcing and the validation surface
can represent those capabilities honestly.

### Context

MiKode's documentation, licensing, and repository-ownership rules apply to this repository,
but the Node.js, TypeScript, code-quality, and testing standards describe capabilities that
`skills` does not have.

The current continuous-integration standard has organization-wide GitHub scope and assumes
a central workflow backed by project-owned validation commands. Applying its existing
documentation profile here would require introducing a Node.js and pnpm toolchain primarily
for governance rather than for the repository's product.

That mismatch is kept explicit instead of manufacturing commands that always pass. This is
a project-specific deviation from the current CI standard, not a replacement for the
cross-project policy.

### Consequences

- The repository stays small and its runtime model remains honest: compatible agents
  consume the skill files directly.
- Node.js, pnpm, TypeScript, ESLint, Vitest, and build infrastructure are not introduced
  without a real repository need.
- The repository does not yet fully satisfy the organization-wide CI standard and relies
  on pull-request review for repository-specific validation.
- If another content-only MiKode repository exposes the same mismatch, the CI standard
  should be revisited through a cross-project ADR rather than accumulating local
  exceptions.
- When automated validation becomes justified, the repository should validate real
  invariants such as skill frontmatter, directory/name consistency, manifest references,
  and internal documentation links.

### Alternative considered

Adding a Node.js and pnpm documentation toolchain immediately would allow the repository to
fit the existing central documentation CI profile. It was not selected because the
repository has no Node.js runtime or package artifact and the toolchain would currently
exist mainly to satisfy the validation mechanism itself.

# Project decisions

## 2026-08-31 — Keep repository tooling tied to real validation needs

### Decision

`skills` remains a content-first repository. It will not add a Node.js or pnpm toolchain
solely to satisfy repository ceremony.

For CI, the Documentation capability applies to this repository. The active MiKode CI
standard separates that validation responsibility from runtime tooling, and `skills`
consumes the reviewed central workflow with the Documentation capability enabled. The
repository therefore gets common Markdown, link, and asset validation without owning a
Node.js or pnpm toolchain.

### Context

The canonical artifacts are Markdown skill definitions and small plugin manifests consumed
directly by host agents. Node.js, TypeScript, build, and test capabilities are not part of
the product today.

The earlier central implementation blocker has been resolved in `Mikode13/.github` under
ADR 0015. Repository-specific tooling is still unnecessary unless a real local invariant
appears that the common Documentation capability does not cover.

### Applicability

This decision applies to repository-wide tooling choices. It does not limit:

- skill content that happens to reference Node.js, TypeScript, or other ecosystems;
- agents' own tool use when executing skills; or
- future repository-specific tooling that validates a real invariant not covered by the
  common Documentation capability.

### Consequences

- The repository stays honest about the capabilities it actually owns.
- Common Markdown, link, and asset checks belong to the central Documentation capability.
- Repository-specific validation may later cover real skill invariants such as frontmatter
  or manifest references when those checks are justified.
- The active CI requirement is satisfied through the central Documentation capability
  without introducing unrelated repository tooling.

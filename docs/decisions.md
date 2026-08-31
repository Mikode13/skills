# Project decisions

## 2026-08-31 — Keep repository tooling tied to real validation needs

### Decision

`skills` remains a content-first repository. It will not add a Node.js or pnpm toolchain
solely to satisfy repository ceremony.

For CI, `skills` conceptually belongs to the documentation profile. The current blocker is
that MiKode's documentation CI path is coupled to pnpm-backed project commands. Until that
profile can validate this repository without manufacturing unused runtime capabilities,
repository-specific automated CI is deferred.

### Context

The canonical artifacts are Markdown skill definitions and small plugin manifests consumed
directly by host agents. Node.js, TypeScript, build, and test capabilities are not part of
the product today.

This is a project-specific implementation gap, not a claim that documentation CI is the
wrong profile. If the same constraint appears in another content-only repository, it should
be handled as a cross-project CI design problem.

### Consequences

- The repository stays honest about the capabilities it actually owns.
- Useful future validation can target skill frontmatter, manifest references, and internal
  links without pretending the repository is an application.
- The current organization-wide CI requirement remains unresolved for this repository until
  the documentation profile supports this shape cleanly.

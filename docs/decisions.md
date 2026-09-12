# Project decisions

## 2026-09-11 — Version the plugin in each functional pull request

### Versioning decision

A GitHub App updates both host manifests inside the functional PR. The PR title determines
the release type, using Conventional Commits:

| Distributed change                                 | Increment | Example from `0.2.0` |
| -------------------------------------------------- | --------- | -------------------- |
| `fix: ...` — a correction                          | patch     | `0.2.1`              |
| `feat: ...` — new functionality                    | minor     | `0.3.0`              |
| A breaking `!` marker or `BREAKING CHANGE:` footer | major     | `1.0.0`              |

The diff determines whether distributed content changed. Editing a skill or its references
requires a release even though the file is Markdown. `docs:`, `ci:`, and other non-release
types are valid for changes outside the bundle; they fail validation if they modify
distributed content. Changes limited to README, project docs, or workflows produce no
release. Branch names and labels do not choose the version.

The central Documentation capability validates the version as part of `CI / required`.
After the definitive main commit passes CI, a second central workflow creates its
`v<version>` tag and GitHub Release with generated notes. The complete inventory and
workflow contract live in the
[central plugin version documentation](https://github.com/Mikode13/.github/blob/main/plugin-version/README.md).

### Why committed versions are needed

Plugin hosts use the version in the committed manifest to distinguish updates. Both
manifests remained at `0.1.0` while new skills were added. This adoption sets them to
`0.2.0`, allowing the accumulated additions to be delivered as a new bundle version.

ADR 0011 covers npm publication, where the registry owns released versions. It does not
cover this content plugin. A separate release PR would add another review cycle; direct
commits to main would need an unnecessary ruleset bypass. The same-PR App commit preserves
normal review and triggers CI, unlike commits made with `GITHUB_TOKEN`.

### Operation and consequences

- The repository remains free of Node.js, pnpm, and local runtime tooling. Bash/`jq`
  implementation and tests belong to the central workflow repository.
- The dedicated App is installed only on this repository with Contents write and Metadata
  access. Its client ID lives in `MIKODE_SKILLS_VERSION_APP_CLIENT_ID`; its private key is
  the Actions secret `MIKODE_SKILLS_VERSION_APP_PRIVATE_KEY`. No credentials belong in Git.
- The privileged workflow runs trusted central code pinned by SHA and reads GitHub data.
  It never executes PR code. The App can write exactly the two prepared manifest files
  through this workflow; its token is restricted to this repository.
- Ready PRs from this repository receive the bot commit. Drafts and forks receive no
  automated write; fork authors update both manifests manually and pass the same check.
- Versions are calculated from the full PR against its merge-base. Release and repair
  changes must include current main; this version check adds no freshness requirement to
  README/CI-only PRs. Organization rulesets may independently require an updated branch.
  The live main ref is authoritative, not the event's potentially stale `base.sha`.
- Main CI validates matching manifests and a single valid increment against the actual
  parent. It does not infer another release type from the squash message. Release retries
  take the successful main CI run ID and
  reuse matching tags/releases; existing tags never move.
- Use `fix: revert ...` for a non-breaking rollback, with a newly calculated version.
  GitHub's default `Revert "..."` title is not supported. Marketplace metadata changes
  also release because that metadata is part of the distributed bundle.
- The first functional CI run may fail until the bot commits the expected versions.
  Editing the PR body reruns full CI because its breaking footer can affect the version.
- The installation PR supplies its bump manually because `pull_request_target` uses the
  default branch's workflow. App commits and updates in already-installed Claude Code
  and Codex clients must be verified after activation before the issue is closed.

No release-PR exception is needed in the AI review standard because the version change is
part of the functional PR.

### Squash history setup

Prefer the PR title and body as squash defaults. The repository currently uses
`COMMIT_OR_PR_TITLE` and `COMMIT_MESSAGES` (checked during adoption review). An
administrator can apply the intended defaults with:

```sh
gh api --method PATCH repos/Mikode13/skills \
  -f squash_merge_commit_title=PR_TITLE \
  -f squash_merge_commit_message=PR_BODY
```

This is an administrative rollout step, not a change applied by these workflows. It keeps
Git history aligned with the reviewed contract; version validation on main works with
either message format. See the
[GitHub repository API](https://docs.github.com/en/rest/repos/repos#update-a-repository).

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

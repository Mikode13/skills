# AGENTS.md

## Purpose

`Mikode13/skills` distributes portable agent skills. Canonical skill bodies live under
`skills/`; plugin manifests expose those same files to supported hosts.

This is currently a content repository, not a Node.js application or library. The reasoning
for that boundary lives in [`docs/decisions.md`](docs/decisions.md).

## Before making changes

1. Read the root `README.md`.
2. Read the applicable **Active** standards from the latest `main` of
   `Mikode13/engineering`. If using a local checkout, sync it with `origin/main` first.
3. Read accepted ADRs only when their reasoning is needed to understand an active rule or
   the requested change.

Apply standards by their declared scope. Do not treat an unrelated standard as applicable
just because it exists.

## Skill rules

Each canonical skill MUST live at `skills/<skill-name>/SKILL.md`.

When changing a skill:

- keep the frontmatter `name` aligned with the directory name;
- make the `description` explicit about what the skill does and when it should be selected;
- keep workflows tool-agnostic unless a host or provider is intrinsic to the task;
- read live MiKode policy at execution time instead of duplicating versions or complete
  rules inside the skill;
- keep distribution manifests pointing at the canonical skill bodies;
- prefer strategic changes that avoid foreseeable technical debt over the smallest change
  that only solves today's case;
- do not add speculative capabilities without a concrete need.

`CLAUDE.md` is a symlink to this file. Do not maintain separate Claude-specific copies of
repository instructions.

## Tooling

Do not introduce Node.js, pnpm, build, test, or type-check infrastructure merely to satisfy
an interface. Add tooling when it validates a real repository invariant or supports an
actual repository capability, then follow the standards that become applicable.

## Before finishing

Verify that:

- changed skill frontmatter is valid and directory names still match;
- distribution manifests still reference the intended skills;
- changed internal links resolve;
- README skill listings and installation instructions remain current; and
- no live engineering rule has been copied unnecessarily into a skill.

# AGENTS.md

## Repository purpose

`Mikode13/skills` distributes portable agent skills. The canonical skill bodies live under
`skills/` and are consumed directly by compatible agents and plugin manifests.

This repository is not a Node.js application or library and does not own a production
runtime. Do not introduce runtime, build, test, or package capabilities merely to make the
repository resemble a source-code project.

## Before making changes

1. Read this file and the root `README.md`.
2. Read `Mikode13/engineering/standards/README.md` and apply only standards whose stated
   scope matches this repository or the change being made.
3. For documentation work, read the active documentation ownership and documentation
   writing standards.
4. Read additional standards only when their scope applies to the task. Do not assume that
   Node.js, TypeScript, package-management, code-quality, or testing standards apply to a
   repository that does not use those capabilities.

The engineering repository is the source of truth. Skills MUST NOT duplicate concrete
standard values that can be read from the current standard at execution time.

## Repository structure

```text
skills/
└── <skill-name>/SKILL.md   Canonical skill bodies

.agents/                   Agent/plugin distribution metadata
.claude-plugin/            Claude Code plugin metadata
.codex-plugin/             Codex plugin metadata
README.md                  Human entry point and installation guide
AGENTS.md                  Agent-specific repository instructions
LICENSE                    Effective repository license
docs/decisions.md          Significant project-specific decisions, when present
```

Distribution metadata may reference the canonical `skills/` directory. It MUST NOT create
independent copies of skill bodies that can drift from the canonical source.

## Editing a skill

Each skill MUST live at `skills/<skill-name>/SKILL.md`.

When creating or changing a skill:

- keep the frontmatter `name` aligned with the directory name;
- keep the frontmatter `description` explicit about what the skill does and when it should
  be selected;
- keep the workflow tool-agnostic unless interaction with a particular host or provider is
  intrinsic to the task;
- read current MiKode standards at execution time instead of hardcoding versions, policy
  values, or complete rule sets into a skill;
- distinguish repository-specific workflow instructions from cross-project engineering
  policy;
- make the smallest coherent change that completes the requested workflow;
- avoid speculative capabilities and placeholder steps.

A skill that applies MiKode standards MUST determine applicability from each standard's
scope before enforcing it. An inapplicable standard is not a violation.

## Documentation

Use the root README for project purpose, installation, usage, current structure, and
navigation. Put significant project-specific decisions in `docs/decisions.md`. Cross-project
policy belongs in `Mikode13/engineering`.

Documentation should be technical, concise, explanatory, and structured for scanning.
Link to authoritative MiKode standards instead of copying their complete content.

## Tooling and validation

This repository currently has no Node.js or pnpm project toolchain. Do not add fake
`package.json` scripts, builds, tests, or type checks for capabilities that do not exist.

If repository-owned automated validation becomes useful, introduce the smallest validation
surface that checks real invariants and document the decision. The current repository-level
CI limitation is recorded in `docs/decisions.md` rather than hidden behind placeholder
commands.

Before completing a change, verify at minimum:

- modified skill frontmatter is valid and names match their directories;
- distribution manifests still reference the intended canonical skills;
- changed Markdown links resolve;
- README skill listings and installation instructions remain current;
- no cross-project standard has been copied into a skill when it can be read live.

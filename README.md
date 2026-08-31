# MiKode Skills

MiKode Skills is a source-available collection of portable
[`SKILL.md`](https://agentskills.io/) workflows for engineering and project management.
Compatible agents consume the skill files directly; this repository does not provide a
Node.js runtime, library, or application.

The canonical skill bodies are shared across Codex, Claude Code, and other agents that
support the skills format. Distribution metadata adapts the same `skills/` directory to
each host instead of maintaining separate copies.

## Current skills

- **mikode-init** — create a MiKode repository by reading the current engineering
  standards and applying only the standards whose scope matches the project.
- **mikode-context** — load the current MiKode engineering context into a session: active
  standards, accepted and proposed ADRs, and conflicts between the open repository and
  those standards.
- **adr-new** — create an Architecture Decision Record in the engineering repository
  through a guided interview in which the user owns the reasoning and the agent verifies
  facts and fills gaps visibly.
- **adr-accept** — accept a proposed ADR by updating its lifecycle, related standards,
  indexes, and supersession metadata consistently.
- **standards-check** — audit a MiKode repository against the live engineering standards,
  first determining which standards actually apply from their declared scope.
- **mikode-code-philosophy** — apply the MiKode coding philosophy when implementing or
  refactoring production code.
- **mikode-code-philosophy-review** — review a change against the MiKode coding philosophy
  and report evidence-backed findings by severity.

## Repository model

```text
skills/
└── <skill-name>/SKILL.md   Canonical skill bodies

.agents/                   Agent/plugin distribution metadata
.claude-plugin/            Claude Code plugin metadata
.codex-plugin/             Codex plugin metadata
README.md                  Human entry point and installation guide
AGENTS.md                  Instructions for agents modifying this repository
LICENSE                    Effective repository license
docs/decisions.md          Significant project-specific decisions
```

The repository is intentionally content-first. It does not add Node.js, pnpm, TypeScript,
build, or test infrastructure unless a real repository capability requires it. The current
tooling boundary and its CI consequence are recorded in
[`docs/decisions.md`](docs/decisions.md).

## Installation

There are two distribution paths. Use the plugin when you want the complete bundle; link
individual skill directories when you want a smaller selection.

### Plugin bundle

The same bundle installs on both Codex and Claude Code: add the repository as a
marketplace, then install the `mikode-skills` plugin from it. The commands differ only by
host CLI and marketplace suffix.

For **Codex**:

```sh
codex plugin marketplace add mikode13/skills
codex plugin add mikode-skills@mikode-skills
```

For **Claude Code**:

```sh
claude plugin marketplace add mikode13/skills
claude plugin install mikode-skills@mikode
```

The equivalent Claude Code in-session commands are `/plugin marketplace add …` and
`/plugin install …`.

For the desktop app of either tool, restart the app, open the Plugins Directory, select
the MiKode Skills marketplace, and install the `mikode-skills` plugin.

### Install individual skills directly

Clone the repository once, then link only the skills you want. This example installs
`adr-new` for Codex and other tools that support the `.agents/skills` layout:

```sh
git clone https://github.com/mikode13/skills.git mikode-skills
mkdir -p ~/.agents/skills
ln -s "$PWD/mikode-skills/skills/adr-new" ~/.agents/skills/adr-new
```

To install every skill independently:

```sh
for skill in "$PWD/mikode-skills/skills"/*; do
  ln -s "$skill" "$HOME/.agents/skills/$(basename "$skill")"
done
```

For Claude Code, use `~/.claude/skills` as the destination:

```sh
mkdir -p ~/.claude/skills
ln -s "$PWD/mikode-skills/skills/adr-new" ~/.claude/skills/adr-new
```

Pull the cloned repository to receive skill updates without reinstalling the links.

### Distribution manifests

The Claude plugin manifest lives under `.claude-plugin/`; the Codex manifest lives under
`.codex-plugin/`; and the repository marketplace metadata lives under `.agents/plugins/`.
These adapters point at the same canonical `skills/` directory.

## Usage

Invoke a skill by name, such as `mikode-init` or `mikode-context`, or describe a task that
matches its frontmatter description. Compatible agents may select matching skills
automatically.

Skill workflows should describe the intended result, constraints, and verification while
the host agent supplies its own filesystem, shell, web, or connector tools.

## Engineering standards

MiKode engineering policy is owned by the
[`Mikode13/engineering`](https://github.com/Mikode13/engineering) repository. This
repository follows standards according to each standard's declared scope rather than
assuming every MiKode standard applies to every repository shape.

The documentation ownership and writing standards apply directly here. Node.js,
TypeScript, package-management, code-quality, and testing rules do not become applicable
merely because they exist elsewhere in MiKode.

The organization-wide CI standard currently has broader scope than this repository's
runtime-free model. `skills` does not manufacture a Node.js toolchain or placeholder
commands to hide that mismatch; the project-specific decision and remaining limitation are
recorded in [`docs/decisions.md`](docs/decisions.md). If the same constraint appears in
other content-only repositories, it should be resolved through the cross-project ADR
process.

## Contributing

Read [`AGENTS.md`](AGENTS.md) before modifying the repository.

Each canonical skill belongs at `skills/<skill-name>/SKILL.md`. Keep the frontmatter name
aligned with the directory, keep descriptions explicit about selection conditions, and
prefer tool-agnostic workflows unless a provider is intrinsic to the task.

Skills that enforce MiKode policy should read the live engineering standards and determine
applicability from their scopes. Do not duplicate versions or complete policy text inside a
skill when the authoritative standard can be read at execution time.

## License

MiKode Skills is source-available software, licensed under the MIT License with the Commons
Clause License Condition v1.0. See [LICENSE](LICENSE). It is not open-source software under
the Open Source Definition because the Commons Clause restricts selling the software.

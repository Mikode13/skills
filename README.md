# MiKode Skills

Portable [`SKILL.md`](https://agentskills.io/) workflows for engineering and project
management. The same skill bodies are designed to work across Codex, Claude Code, and
other agents that support the skills format.

## Skills

- **mikode-init** — create a new MiKode project repository from scratch, applying the
  current engineering standards (licensing, pnpm, Node.js, TypeScript, ESLint,
  Prettier). Skills read the standards live from the engineering repo, so scaffolds
  never go stale.
- **mikode-context** — load the current MiKode engineering context into any session:
  active standards, accepted and proposed ADRs, and conflicts between the open
  repository and the standards.
- **adr-new** — create a new Architecture Decision Record in the engineering
  repository through a guided interview: the user provides the reasoning, the agent
  verifies facts and fills gaps visibly.
- **adr-accept** — accept a proposed ADR: flip statuses, activate the related
  standard, update every index, and handle supersession consistently.
- **standards-check** — lint a MiKode repository against the current standards as a
  final gate: undocumented deviations fail until they are reverted, covered by a
  documented exception, or proposed as an ADR.

## Installation

There are two distribution paths. Use the plugin when you want the complete bundle;
link individual skill directories when you want a smaller selection.

### Codex plugin bundle

This repository now includes Codex’s required `.codex-plugin/plugin.json` and a repo
marketplace at `.agents/plugins/marketplace.json`. The object with `name`, `version`,
`description`, and `skills` belongs in `plugin.json`; `marketplace.json` is the catalog
that exposes that plugin.

Add the repository as a Codex marketplace:

```sh
codex plugin marketplace add mikode13/skills
```

For the Codex desktop app, restart the app, open the Plugins Directory, select the
MiKode Skills marketplace, and install the `mikode-skills` plugin. For Codex CLI or
other tools that load the universal skills directory, use the direct installation
below.

### Install individual skills directly

Clone the repository once, then link only the skills you want. The following example
installs `adr-new` for Codex and other tools that support the `.agents/skills` layout:

```sh
git clone https://github.com/mikode13/skills.git mikode-skills
mkdir -p ~/.agents/skills
ln -s "$PWD/mikode-skills/skills/adr-new" ~/.agents/skills/adr-new
```

To install all skills independently, link each directory instead:

```sh
for skill in "$PWD/mikode-skills/skills"/*; do
  ln -s "$skill" "$HOME/.agents/skills/$(basename "$skill")"
done
```

For Claude Code, use the same commands with `~/.claude/skills` as the destination:

```sh
mkdir -p ~/.claude/skills
ln -s "$PWD/mikode-skills/skills/adr-new" ~/.claude/skills/adr-new
```

This direct-link approach also works with other tools that document support for
`.agents/skills`. Pull the cloned repository to receive updates without reinstalling
the links.

### Claude Code plugin bundle

The existing Claude Code marketplace remains available:

```text
/plugin marketplace add mikode13/skills
/plugin install mikode-skills@mikode
```

The Claude plugin manifest is kept under `.claude-plugin/`; the Codex manifest is kept
under `.codex-plugin/`. Both point at the same `skills/` directory.

## Usage

Invoke a skill by name, such as `mikode-init` or `mikode-context`, or describe a task
that matches its frontmatter description. Compatible agents may choose matching
skills automatically.

Keep skill bodies tool-agnostic: workflows should describe the intended result and
verification, while the host agent supplies its own filesystem, shell, web, or
connector tools.

## Contributing

Skills follow the same philosophy as the engineering repository: they reference the
standards as the source of truth instead of duplicating their values. When a standard
changes, a skill only needs updating if its workflow changed, not its data.

## License

MiKode Skills is source-available software, licensed under the MIT License with the
Commons Clause License Condition v1.0. See [LICENSE](LICENSE). It is not open-source
software under the Open Source Definition: the Commons Clause restricts selling the
software. You may use, copy, modify, and redistribute the software within the limits
of the complete license.

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
- **adr-new** — create a new Architecture Decision Record through a guided interview,
  publish it behind a Draft pull request for safe review, and finalize it after one
  explicit user approval.
- **adr-accept** — accept a proposed ADR: flip statuses, activate the related
  standard, update every index, handle supersession consistently, and mark an approved
  Draft pull request ready without merging it.
- **standards-check** — lint a MiKode repository against the current standards as a
  final gate: undocumented deviations fail until they are reverted, covered by a
  documented exception, or proposed as an ADR.
- **mikode-code-philosophy** — apply the MiKode coding philosophy when
  implementing or refactoring: strategic design, strong boundaries, explicit
  behaviour, and meaningful tests over speed or generated-code verbosity.
- **mikode-code-philosophy-review** — review a change against the MiKode coding
  philosophy and report prioritized findings (blocker / should fix / suggestion)
  without inventing problems merely to provide feedback.

## Installation

There are two distribution paths. Use the plugin when you want the complete bundle;
link individual skill directories when you want a smaller selection.

### Plugin bundle

The same bundle installs on both Codex and Claude Code: add the repository as a
marketplace, then install the `mikode-skills` plugin from it. The steps are identical
across the two tools — only the CLI binary and the marketplace suffix differ.

For **Codex**:

```sh
codex plugin marketplace add mikode13/skills
codex plugin add mikode-skills@mikode-skills
```

For **Claude Code** (these are shell commands; the equivalent in-session slash
commands are `/plugin marketplace add …` and `/plugin install …`):

```sh
claude plugin marketplace add mikode13/skills
claude plugin install mikode-skills@mikode
```

For the desktop app of either tool, restart the app, open the Plugins Directory,
select the MiKode Skills marketplace, and install the `mikode-skills` plugin. For the
CLI or other tools that load the universal skills directory, use the direct
installation below.

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

### Manifests

The Claude plugin manifest is kept under `.claude-plugin/`; the Codex manifest is kept
under `.codex-plugin/`, with the repo marketplace at `.agents/plugins/marketplace.json`.
The object with `name`, `version`, `description`, and `skills` belongs in each
`plugin.json`; the `marketplace.json` files are the catalogs that expose the plugin.
All of them point at the same `skills/` directory.

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

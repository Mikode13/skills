# MiKode Skills

Portable [`SKILL.md`](https://agentskills.io/) workflows for engineering and project
management. The same canonical skill bodies are distributed across Codex, Claude Code, and
other compatible agents.

## Skills

- **mikode-init** — create a MiKode repository from the current engineering standards,
  applying only the standards that match the project.
- **mikode-context** — load the current MiKode engineering context into a session.
- **adr-new** — create a new Architecture Decision Record through a guided interview,
  publish it behind a Draft pull request for safe review, and finalize it after one
  explicit user approval.
- **adr-accept** — accept a proposed ADR, update its related standards and indexes,
  and finish the reviewed Draft pull request without merging it.
- **standards-check** — audit a repository against applicable active MiKode standards.
- **mikode-documentation** — create, update, or review technical documentation using the
  current MiKode documentation policy.
- **mikode-code-philosophy** — apply the MiKode coding philosophy when implementing or
  refactoring production code.
- **mikode-code-philosophy-review** — review a change against the MiKode coding philosophy
  and report evidence-backed findings by severity.
- **mikode-review** — coordinate a complete PR or local-change review across five
  perspectives, load specialist guidance by risk, and verify findings before returning
  `clean`, `blocked`, or `incomplete`.
- **mikode-architecture-review** — review responsibilities, boundaries, public contracts,
  and information hiding in a module, selected code, local changes, or a pull request,
  using the project's architecture documentation and relevant consumers.
- **mikode-security-review** — review a module, selected code, local changes, or a pull
  request for exploitable security defects and unsafe defaults across authentication,
  secrets, untrusted input, network, supply chain, CI, and sandbox boundaries.

## Installation

There are two distribution paths. Use the plugin when you want the complete bundle; link
individual skill directories when you want a smaller selection.

### Plugin bundle

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

For the desktop app of either tool, restart the app, open the Plugins Directory, select the
MiKode Skills marketplace, and install the `mikode-skills` plugin.

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
All of them point at the canonical `skills/` directory.

## Usage

Invoke a skill by name, such as `mikode-init` or `mikode-context`, or describe a task that
matches its frontmatter description. Compatible agents may select matching skills
automatically.

Skill workflows describe the intended result, constraints, and verification while the host
agent supplies its own filesystem, shell, web, or connector tools.

## Contributing

Read [`AGENTS.md`](AGENTS.md) before modifying the repository. MiKode engineering policy is
owned by [`Mikode13/engineering`](https://github.com/Mikode13/engineering); skills should
read the current policy instead of duplicating it.

Project-specific decisions are recorded in [`docs/decisions.md`](docs/decisions.md).

## License

MiKode Skills is source-available software, licensed under the MIT License with the Commons
Clause License Condition v1.0. See [LICENSE](LICENSE). It is not open-source software under
the Open Source Definition because the Commons Clause restricts selling the software.

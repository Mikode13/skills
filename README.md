# MiKode Skills

Shared [Claude Code](https://claude.com/claude-code) skills for the MiKode team. Each
skill packages a repeatable workflow so it is executed the same way by everyone,
against the current state of the [MiKode engineering standards](https://github.com/mikode13/engineering).

## Skills

- **mikode-init** — create a new MiKode project repository from scratch, applying the
  current engineering standards (licensing, pnpm, Node.js, TypeScript, ESLint,
  Prettier). Skills read the standards live from the engineering repo, so scaffolds
  never go stale.
- **mikode-context** — load the current MiKode engineering context into any session:
  active standards, accepted and proposed ADRs, and conflicts between the open
  repository and the standards.
- **adr-new** — create a new Architecture Decision Record in the engineering
  repository with correct numbering, template, statuses, and index updates.
- **wiki-check** — stub; becomes a documentation-usage checker once the wiki
  conventions are defined.

## Installation

This repository is a Claude Code plugin marketplace. In Claude Code:

```text
/plugin marketplace add mikode13/skills
/plugin install mikode-skills@mikode
```

Updates ship by pulling the marketplace; reinstall or update the plugin to pick up new
skill versions.

Alternatively, without the plugin system, clone the repository and symlink the skill
directories into your personal skills folder:

```sh
git clone https://github.com/mikode13/skills.git
ln -s "$(pwd)/skills/"* ~/.claude/skills/
```

## Usage

Invoke a skill by name in Claude Code, for example:

```text
/mikode-init
/mikode-context
```

Claude also loads a skill automatically when a task matches its description.

## Contributing

Skills follow the same philosophy as the engineering repository: they reference the
standards as the source of truth instead of duplicating their values. When a standard
changes, a skill only needs updating if its workflow changed, not its data.

## License

MiKode Skills is source-available software, licensed under the MIT License with the
Commons Clause License Condition v1.0. See [LICENSE](LICENSE). It is not open-source
software under the Open Source Definition: the Commons Clause restricts selling the
software. You may use, copy, modify, and redistribute it within the limits of the
complete license.

---
name: mikode-init
description: Create a new MiKode repository from scratch by reading the live engineering standards and applying only the standards whose declared scope matches the project. Use when the user wants to start, bootstrap, or scaffold a MiKode repository without inventing capabilities the project does not need.
---

# Initialize a MiKode project repository

Create a new repository that follows the current MiKode engineering standards applicable
to its actual project shape. Never hardcode standard values from this skill and never turn
a documentation, content, or non-Node repository into a Node.js project merely because
Node-oriented standards exist.

## 1. Load the engineering source of truth

Locate `mikode13/engineering` through a current local checkout or a temporary clone.

Read `standards/README.md` first. Then read the standards whose scopes may match the
project, including the documentation ownership and documentation writing standards for a
normal MiKode repository.

Read templates only when the applicable standard calls for them.

Treat:

- applicable `Active` standards as mandatory;
- applicable `Draft` standards as non-binding guidance that must be reported to the user;
- standards outside the project scope as not applicable.

Do not apply a standard before checking its `Scope` section.

## 2. Gather the project facts

Ask only for facts that cannot be derived from the user's request. Resolve these before
scaffolding:

1. Project name and purpose.
2. Repository shape: for example library, application, service, configuration package,
   documentation/content repository, agent/skill repository, or another explicit type.
3. Runtime and languages, if any.
4. Whether it will be published, packaged, deployed, or only consumed directly from the
   repository.
5. Visibility: public or private.
6. Effective license: confirm the MiKode source-available model when its licensing standard
   applies, or record the chosen exception/other license when it does not.

Ask additional questions only when they materially change which standards apply.

## 3. Build an applicability plan

Before creating files, summarize the relevant standards internally as:

```text
standard → status → applicable? → reason
```

Examples:

- A Node.js TypeScript library may need package management, Node.js, TypeScript, code
  quality, formatting, testing, documentation, Git workflow, CI, and licensing standards.
- A Markdown skill repository may need documentation, GitHub governance, and licensing
  policy without needing TypeScript, ESLint, Vitest, or a Node.js runtime.
- A repository outside the Node.js/pnpm ecosystem should use the exceptions defined by the
  Git workflow and CI standards rather than manufacturing Node-specific capabilities.

If an active standard claims the project in scope but the available implementation profile
cannot represent the repository honestly, surface the conflict. Do not hide it with
placeholder commands; document or escalate it according to the standard.

## 4. Scaffold only real capabilities

Every normal MiKode repository should receive the baseline artifacts required by the live
documentation standard, using current templates when one exists. At minimum, create the
human entry point, agent instructions, and effective license required for that project.

Then add only configuration justified by applicable standards and actual capabilities.
Examples include:

- `package.json`, lockfile, pnpm configuration, and package-manager guard only when the
  project actually uses the Node.js/npm ecosystem;
- `.nvmrc` and `engines` only when the repository has a Node.js runtime or applicable
  Node-based tooling;
- `tsconfig.json` only for TypeScript source or configuration in scope;
- ESLint and Prettier configuration only when their standards apply to repository-owned
  content;
- test and build scripts only when the project has meaningful tests or a build;
- CI using the applicable repository profile and honest project-owned validation commands;
- `docs/decisions.md` only when the project records its first significant project-specific
  decision, never as an empty placeholder.

Do not add fake `test`, `build`, `typecheck`, or validation commands that exist only to
satisfy an interface.

## 5. Write the initial documentation

Follow the active documentation writing standard.

The README should provide or link to the information needed to understand:

- what the project is and why it exists;
- its current status and meaningful limitations;
- how to install or prepare it when installation exists;
- how to run, consume, or use it successfully for the first time;
- its important architecture or components when they help understanding.

Keep agent-only repository instructions in `AGENTS.md`. Link to authoritative MiKode
standards instead of copying complete policy text.

## 6. Validate and report

Run every validation command that genuinely belongs to the scaffold. Use
`standards-check` as the final policy audit.

If an applicable active standard cannot be satisfied without inventing an unused
capability, stop and report the scope/implementation conflict rather than silently
creating ceremony.

Report:

- repository URL;
- repository shape and capabilities;
- applicable standards and their statuses;
- standards deliberately classified as not applicable when that distinction matters;
- validation performed;
- any unresolved deviation or documented exception.

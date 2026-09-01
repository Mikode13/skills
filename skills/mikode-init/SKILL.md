---
name: mikode-init
description: Create a new MiKode repository from scratch using the latest accepted engineering policy. Use when the user wants to start, bootstrap, or scaffold a MiKode repository while keeping the current TypeScript/Node/pnpm defaults for code projects and avoiding runtime tooling for content-only repositories.
---

# Initialize a MiKode repository

Create a repository that follows the current MiKode standards that actually apply to it.
Do not copy concrete policy values into this skill.

## 1. Load current policy

Use the latest `main` from `Mikode13/engineering` as the source of truth. A local checkout
may be used only after syncing it with `origin/main`.

Read `standards/README.md`, then the **Active** standards whose scopes may apply. Use
accepted ADRs when their reasoning is needed. Proposed ADRs and draft standards are not
binding unless the user explicitly asks to trial them.

## 2. Resolve the project shape

Gather only facts that are not already clear from the request:

1. project name and purpose;
2. repository shape: library, application, service, config package, documentation/content,
   agent/skill repository, or another explicit type;
3. visibility and publication/deployment intent when relevant;
4. effective license when it cannot be derived from the applicable standard.

Use `Mikode13/<project-name>` as the default GitHub repository. For publishable npm
packages, use `@mikode13/<project-name>` as the default package name. Preserve an explicit
different owner, repository name, package name, or non-published choice from the user.

For code libraries, applications, services, and configuration packages, default to MiKode's
current TypeScript, Node.js, and pnpm stack unless the user or a real constraint requires a
different ecosystem. Do not apply that default to documentation, content, or skill
repositories that have no runtime.

## 3. Create and scaffold from applicable standards

Create the GitHub repository with the resolved owner, name, and visibility, then use it as
the target for the scaffold.

Check each candidate standard's `Scope` before applying it. Take versions, configuration,
templates, commands, and exceptions directly from the live standard.

Create the baseline repository artifacts required by the active documentation standard.
When `AGENTS.md` is created, also create `CLAUDE.md` as a symlink to `AGENTS.md`; do not
maintain independent Claude instructions.

Add package management, runtime, TypeScript, linting, testing, build, CI, or publishing
configuration only when the repository shape and applicable standards justify those
capabilities. Do not create placeholder commands merely to satisfy an interface.

Create `docs/decisions.md` only when the repository has its first significant
project-specific decision.

If an active standard includes the project in scope but its available implementation cannot
represent the repository cleanly, report the conflict instead of hiding it with artificial
tooling.

## 4. Document, validate, and publish the scaffold

Write repository documentation according to the active MiKode documentation standards;
do not restate those rules here.

Run every validation command that genuinely belongs to the scaffold and use
`standards-check` as the final policy audit.

After validation passes, create the initial commit and push it to the GitHub repository.

Report the repository URL, project shape, applicable active standards, validation performed,
and any unresolved deviation or permitted exception.

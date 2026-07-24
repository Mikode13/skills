---
name: mikode-init
description: Create a new MiKode project repository from scratch, applying the current MiKode engineering standards (licensing, pnpm, Node.js, TypeScript, ESLint, Prettier). Use when the user wants to start, bootstrap, or scaffold a new MiKode repo or package.
---

# Initialize a MiKode project repository

Create a new git repository that complies with the current MiKode engineering
standards. Never hardcode standard values from this skill: always read the live
standards so the scaffold reflects the current policy.

## 1. Locate the engineering repository

The source of truth is `mikode13/engineering`.

1. Look for a local checkout at `../engineering` relative to the current directory, or
   at `~/Documents/Mikode/Projects/engineering`.
2. If no local checkout exists, clone it to a temporary directory:
   `gh repo clone mikode13/engineering <tmpdir>`.

Read before scaffolding:

- `standards/licensing.md` and `templates/LICENSE.template`
- `standards/package-management.md`
- `standards/nodejs-version.md`
- `standards/typescript.md`
- `standards/code-quality.md`
- `standards/code-formatting.md`

Treat `Active` standards as mandatory. For `Draft` standards, apply them but tell the
user which applied standards are still drafts.

## 2. Gather project facts

Ask the user (do not guess):

- Repository name and one-line description.
- Visibility (public or private).
- Project type: Node library, browser library, React library, or config package.
- Licensor name and confirmation that the MiKode source-available license applies, or
  which exception license to use instead.

## 3. Create and scaffold

1. `gh repo create mikode13/<name>` with the chosen visibility, clone it locally.
2. Scaffold, taking every concrete value (versions, ranges, options) from the
   standards documents read in step 1:
   - `LICENSE` — completed from `templates/LICENSE.template`, all placeholders
     replaced; run the validation checklist in `standards/licensing.md`.
   - `package.json` — name, description, `"type": "module"`, pinned `packageManager`,
     `engines`, the `preinstall` package-manager guard, and the standard scripts
     (format, lint, typecheck/build, test placeholder).
   - `.nvmrc` — per the Node.js version standard.
   - `pnpm-workspace.yaml` — only if the standards require settings there.
   - `tsconfig.json` — extending the shared config for the chosen project type, or the
     documented equivalent options if the shared package is not yet published.
   - `eslint.config.js` and Prettier configuration — same rule: shared package if
     published, documented equivalent otherwise.
   - `.gitignore` — `node_modules/`, `dist/`, editor and OS noise.
   - `README.md` — name, description, install/usage placeholder, and the
     source-available license statement required by the licensing standard.
3. Install dependencies with pnpm and verify: format check, lint, and type check all
   pass on the empty scaffold.
4. Initial commit and push.

## 4. Report

Tell the user: the repo URL, which standards were applied (with their statuses), any
placeholder or TODO left in the scaffold, and any standard that could not be applied
with the reason.

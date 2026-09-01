# ADR 0015 Acceptance Trial Report
## Mikode13/skills Repository

**Trial Date:** 2026-09-01  
**Repository:** Mikode13/skills (content-only skill definitions)  
**ADR Reference:** [ADR 0015: Compose CI by capability](https://github.com/Mikode13/engineering/pull/11)  
**CI Standard Reference:** [Draft Continuous Integration Standard](https://github.com/Mikode13/engineering/pull/11)

---

## Executive Summary

**Trial Result:** ✅ **SUCCESSFUL CLASSIFICATION** | ❌ **CENTRAL CI GAP IDENTIFIED**

The trial correctly identified that `Mikode13/skills` requires only the **Documentation capability** and successfully avoided adding unrelated capabilities or tooling. However, the trial exposed a critical gap in the current central CI contract: the `docs` profile cannot provide Documentation validation independently of Node.js, pnpm, and `package.json` — exactly the constraint the ADR seeks to decouple.

---

## Part 1: Repository Analysis

### Facts About Skills Repository

| Property | Value |
| -------- | ----- |
| Type | Content-only repository |
| Primary artifacts | SKILL.md files (Markdown skill definitions) |
| Documentation | README.md, docs/decisions.md, internal links |
| Executable code | None |
| Test suite | None |
| Build process | None |
| npm package | No |
| Application | No |

### Capability Applicability Classification

**Applied:** CI Standard capability table (section: Capability selection)

| Capability | Applicable | Evidence |
| ----------- | ---------- | -------- |
| **Documentation** | ✅ YES | Documentation and content are first-class artifacts: 7 SKILL.md files, README.md, ADR documentation in docs/decisions.md |
| Source | ❌ NO | No executable source code; repository contains only Markdown content and plugin manifests |
| Tests | ❌ NO | No mandatory test suite; repository has no runtime to test |
| Build | ❌ NO | No build process; skills are distributed as-is |
| Package | ❌ NO | Not an npm package; not published to npm registry |
| End-to-end | ❌ NO | Not an application; no end-to-end boundaries |

**Classification Result:** Only **Documentation** capability applies.

---

## Part 2: Central CI Contract Inspection

### Current Implementation (SHA: 78523ec52c5a598be22e8682cee47409bbe9b4a5)

**Central Workflow:** `Mikode13/.github/.github/workflows/ci.yml`  
**Profile:** `docs`

### Docs Profile Behavior (Current)

```yaml
docs:
  name: documentation
  if: ${{ inputs.profile == 'docs' }}
  needs: contract
  runs-on: ubuntu-latest
  steps:
    - name: Install pnpm and Node.js 24
    - name: Install dependencies
      run: pnpm install --frozen-lockfile
    - name: Validate documentation
      run: pnpm run docs:check
```

### Contract Validation Step (Current)

The workflow performs validation that rejects content-only repositories:

```bash
# From workflow contract validation:
if [[ ! -f "$WORKING_DIRECTORY/package.json" ]]; then
  echo "::error::No package.json found in $WORKING_DIRECTORY"
  exit 1
fi

if [[ ! -f "$LOCKFILE_PATH" ]]; then
  echo "::error::No pnpm lockfile found at $LOCKFILE_PATH"
  exit 1
fi
```

---

## Part 3: Central CI Gap Analysis

### The Problem

The current CI contract **violates ADR 0015's constraint** for the Documentation capability:

**CI Standard Requirement:**
> "The Documentation capability MUST NOT inherently require Node.js, pnpm, `package.json`, or a pnpm lockfile."

**Current Implementation Reality:**
The `docs` profile unconditionally:
1. ✅ Installs Node.js 24
2. ✅ Installs pnpm 11.17.0
3. ✅ Requires `package.json` (validation fails without it)
4. ✅ Requires `pnpm-lock.yaml` (validation fails without it)
5. ✅ Runs `pnpm run docs:check` (assumes a project-defined command exists)

### Impact on Skills Repository

**Skills cannot adopt the new CI contract because:**

1. ❌ Skills has no `package.json` → **contract validation fails immediately**
2. ❌ Skills has no `pnpm-lock.yaml` → **contract validation fails immediately**
3. ❌ Skills has no `pnpm run docs:check` command → **no executable contract**
4. ❌ Adding these files would contradict the ADR 0014 decision already accepted in this repository

### What Would Be Manufactured If We Tried

If skills attempted to comply with the current `docs` profile, it would need:

```
package.json                  ← Contradicts content-only design (ADR 0014)
pnpm-lock.yaml               ← Contradicts content-only design
.npmrc                        ← Contradicts content-only design
pnpm-workspace.yaml (maybe)  ← Contradicts content-only design
scripts.docs:check           ← No actual documentation tooling needed
```

This is **exactly** the problem the ADR 0015 proposals to solve: repositories should not manufacture tooling to satisfy a profile contract.

---

## Part 4: Proposed CI Caller for Skills

To prepare for the capability-based contract, I've created a placeholder CI caller:

**File:** `.github/workflows/ci.yml`

```yaml
name: CI

on:
  pull_request:
    branches: [main]
  push:
    branches: [main]

concurrency:
  group: ${{ github.workflow }}-${{ github.event.pull_request.number || github.ref }}
  cancel-in-progress: true

permissions:
  contents: read
  pull-requests: read

jobs:
  ci:
    name: CI
    # ADR 0015 trial: invoke capability-based central CI
    uses: Mikode13/.github/.github/workflows/ci.yml@PLACEHOLDER_SHA
    with:
      capabilities: "documentation"
      # When implemented, this syntax makes the contract clear:
      # - No Node.js/pnpm needed for documentation validation
      # - No other capabilities implied or inherited
    secrets: inherit
```

This caller:
- ✅ Specifies only applicable capabilities (Documentation)
- ✅ Omits capabilities not applicable (Source, Tests, Build, Package, End-to-end)
- ✅ Makes the contract explicit rather than inferring tooling from a profile
- ✅ Awaits capability-based central workflow implementation

---

## Part 5: Trial Findings

### ✅ Successfully Validated

1. **Correct capability classification:** Skills requires only Documentation, not Source/Tests/Build/Package/E2E
2. **No unnecessary capabilities added:** Trial avoided manufacturing tooling to fit an available preset
3. **Central CI contract discovered:** Trial inspected and understood the current profile-based implementation
4. **Clear gap identified:** Trial explicitly reported why the current contract cannot work for skills without violating its own design principle

### ❌ Central CI Gap Identified

**Gap:** The `docs` profile cannot provide Documentation capability independently of Node.js/pnpm

**Impact:**
- Skills repository is currently blocked from adopting centralized CI
- Any content-only MiKode repository faces the same blocker
- The central workflow must be updated to support capability-based selection before adoption is possible

**Related Issue:** This gap is documented in the ADR 0014 (and this PR's ADR 0015) context:
> "The current `docs` profile works for `engineering`, where Node.js/pnpm run real documentation validators, but it also makes that tooling part of the profile contract."

---

## Recommendations for ADR 0015 Acceptance

### For the Central CI Implementation (Mikode13/.github)

1. **Decouple Documentation capability from Node.js/pnpm:**
   - Create a Documentation capability that validates Markdown, links, and structure independently
   - Allow repositories to optionally extend it with Node.js-based tooling (like `Mikode13/engineering` does)
   - Do not require `package.json` or `pnpm-lock.yaml` for the common contract

2. **Version capability contracts with workflow revisions:**
   - Make supported capabilities discoverable from each pinned workflow SHA
   - Ensure callers can inspect what's available before updating

3. **Preserve migration path:**
   - Keep `docs` preset as a compatibility shim for existing consumers
   - Migrate new adopters to capability-based selection
   - Allow existing callers to update gradually

### For Documentation Repository Adoption

Skills repository is ready to adopt the capability-based contract once:
1. Mikode13/.github implements the capability-based workflow with Documentation independent of Node.js/pnpm
2. Skills updates the caller workflow to use `capabilities: "documentation"` instead of the placeholder
3. Central workflow validates that Documentation capability is satisfied without requiring package.json or pnpm-lock.yaml

---

## Conclusion

**ADR 0015 achieves its design goal:** Decoupling repository validation responsibilities from implementation tooling. The trial successfully proved this can work, and identified exactly what needs to change in the central CI implementation to unblock content-only repositories like skills from adopting composable CI.

The ADR is **ready for acceptance** from an architectural perspective. The central CI implementation must follow to realize the benefits.

**Trial Verdict:** ✅ **RECOMMEND ACCEPTANCE** of ADR 0015 pending central workflow implementation.

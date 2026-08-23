# MiKode Code Review Skill

---

name: mikode-code-review
description: Review code against the MiKode coding philosophy. Use after implementation or refactoring to identify correctness issues, architectural coupling, unnecessary complexity, weak tests, avoidable technical debt, scope creep, and generated-code patterns. Produce prioritized, actionable findings without inventing problems merely to provide feedback.

---

# Review code the MiKode way

Review the implementation as a technical reviewer, not as a code generator.

The purpose of this skill is to determine whether the change is:

- Correct.
- Well tested.
- Maintainable.
- Properly bounded.
- Explicit.
- Consistent with the existing architecture.
- Free from unnecessary complexity.
- Consistent with the MiKode coding philosophy.

Before reviewing:

1. Read the repository `AGENTS.md`.
2. Read the applicable MiKode engineering standards.
3. Understand the requested change and its intended scope.
4. Inspect the relevant surrounding code.
5. Use `mikode-code` as the implementation philosophy when available.

Do not review code in isolation when repository context materially affects the decision.

## Review priorities

Review in this order:

1. Correctness.
2. Critical-path coverage.
3. Architectural boundaries.
4. Scope.
5. Maintainability.
6. Coupling and dependencies.
7. Simplicity.
8. Naming and readability.
9. Minor cleanup.

Do not prioritize stylistic preferences over correctness or architecture.

Formatting and linting concerns that are already enforced automatically should not dominate the review.

## Finding severity

Classify findings using these levels.

### BLOCKER

Use when the change should not be accepted as-is.

Examples:

- Incorrect behaviour.
- Broken public contract.
- Security issue.
- Data corruption risk.
- Missing critical error handling.
- Important regression.
- Architectural boundary violated in a way that creates significant coupling.
- A test suite that gives false confidence for critical behaviour.

### SHOULD FIX

Use for meaningful design or maintainability problems that should normally be resolved before considering the change complete.

Examples:

- Avoidable technical debt.
- Infrastructure leaking into application or domain code.
- Unnecessary public API exposure.
- Weak or missing tests for important behaviour.
- Unnecessary dependency.
- Mutation that makes state difficult to reason about.
- Scope expansion that should be separated.
- Significant duplication with a clear shared responsibility.
- Artificial abstraction or unnecessary indirection.

### SUGGESTION

Use for improvements that are valuable but not required.

Examples:

- Better naming.
- Small simplification.
- A clearer signature.
- Minor readability improvement.
- Follow-up refactor outside the current scope.

Do not inflate severity merely to make the review appear more useful.

## 1. Verify the requested behaviour

Confirm that the implementation actually solves the requested problem.

Check:

- Happy path.
- Relevant failure paths.
- Boundary conditions.
- Invalid state handling.
- Behaviour under realistic exceptional conditions.
- Backwards compatibility when applicable.

Do not infer correctness from compilation or green tests alone.

If the implementation solves a different problem than requested, report it.

## 2. Review the tests

Tests must prove meaningful behaviour.

Check whether:

- Critical paths are covered.
- Important failures are covered.
- Tests assert observable behaviour rather than implementation details.
- Regression tests would actually fail without the fix.
- Tests remain valuable after internal refactoring.

For bugs and meaningful behavioural changes, prefer evidence that:

```text
Without implementation
→ relevant test fails

With implementation
→ relevant test passes
```

Flag tests that remain green even when the behaviour they claim to validate is removed.

Do not request tests for private methods merely because they exist.

Direct tests are appropriate when an internal abstraction owns meaningful behaviour of its own.

## 3. Protect architectural boundaries

Check whether the implementation introduces unnecessary coupling.

Look for:

- Domain or application code depending directly on infrastructure.
- Third-party types leaking into MiKode public contracts.
- Direct dependency on a provider where a meaningful port should exist.
- Framework-specific concepts escaping their intended layer.
- Consumers knowing implementation details they do not need.

Prefer:

```text
Consumer
   ↓
MiKode contract
   ↓
Adapter
   ↓
External implementation
```

Do not demand interfaces mechanically.

A contract should represent a real boundary, not satisfy a pattern checklist.

## 4. Review the public API

Public APIs should expose only what current consumers require.

Look for:

- Internal errors exported unnecessarily.
- Helpers exported without consumers.
- Provider-specific configuration exposed through generic contracts.
- Public methods added only for testing.
- Types made public for hypothetical future use.

If there is no consumer, the default should be to keep the concept internal.

The public API should normally be smaller than the internal implementation surface.

## 5. Detect scope creep

Compare the diff with the requested task.

Look for:

- Unrelated refactors.
- Renames unrelated to the feature.
- Formatting large unrelated areas.
- Opportunistic architectural changes.
- Behaviour changes outside the requirement.

Do not automatically condemn a trivial adjacent cleanup.

Flag unrelated changes when they increase review surface, risk, or conceptual scope.

If unrelated problematic code was correctly left untouched and reported separately, that is desirable behaviour.

## 6. Review abstractions

Look for both missing and unnecessary abstractions.

### Possible unnecessary abstraction

Examples:

- Interface with no architectural boundary.
- Factory that always creates one fixed implementation and adds no meaningful policy.
- Wrapper that only renames another API.
- Generic type introduced for one concrete case without a clear need.
- Helper class with no coherent responsibility.
- Layers that merely forward calls.

### Possible missing abstraction

Examples:

- Multiple consumers coupled directly to infrastructure.
- Third-party semantics leaking through several modules.
- The same responsibility appearing repeatedly.
- A boundary that is difficult to replace or test.

Do not extract code based only on visual similarity.

Duplication may be tolerated twice while the correct abstraction remains unclear.

On the third occurrence, review whether a real shared responsibility has emerged.

The goal is not fewer files or more abstractions.

The goal is lower complexity and clearer ownership.

## 7. Prefer composition

Look for inheritance that primarily exists to share implementation.

Ask whether focused collaborators composed together would provide:

- Better flexibility.
- Clearer ownership.
- Lower coupling.
- Easier testing.

Do not flag inheritance when it models a genuine and useful hierarchy better than composition.

Composition is the default, not an absolute prohibition.

## 8. Review mutability

Prefer immutable transformations where practical.

Look for:

- Mutated function parameters.
- Mutation of caller-owned objects.
- Shared mutable state without clear ownership.
- State changes that are difficult to trace.

Do not flag mutation merely because mutation exists.

Mutation is acceptable when:

- It is intentional.
- Ownership is clear.
- It materially simplifies the abstraction.
- The abstraction itself is inherently stateful.

## 9. Review error handling

Check that errors:

- Fail fast when an important invariant is violated.
- Are meaningful at the layer where they are thrown.
- Are caught only where recovery, translation, enrichment, logging, or cleanup is possible.
- Are not silently swallowed.
- Do not expose unnecessary implementation details publicly.

Flag:

```ts
try {
  await operation();
} catch (error) {
  throw error;
}
```

when the catch adds no value.

Do not require defensive code for impossible or irrelevant hypothetical situations.

Do require handling for conceivable invalid states that could realistically occur.

## 10. Review dependencies

New third-party dependencies require justification.

Check whether the problem could reasonably be solved using:

1. Platform or language capabilities.
2. Existing MiKode abstractions.
3. Existing project dependencies.
4. Only then, a new dependency.

Consider:

- Coupling.
- Maintenance.
- Security.
- Upgrade burden.
- Bundle/runtime cost where relevant.
- Whether the dependency introduces provider-specific concepts into the codebase.

Do not reject a dependency simply because it is external.

Reject or question it when its value does not justify its cost.

## 11. Review logging and external services

Application code should not depend directly on a logging destination when logging is an infrastructure concern.

Flag direct coupling such as:

```text
application logic
→ Sentry
```

when the project should instead have:

```text
application logic
→ Logger contract
→ Sentry adapter
```

The same principle applies to comparable infrastructure providers.

## 12. Detect unnecessary configuration

Check whether configuration represents something that may meaningfully vary.

Good configuration:

```text
maxRetries = 3
```

with the possibility of a legitimate override.

Question configuration that:

- Has one permanent value.
- Exists only for hypothetical future flexibility.
- Makes construction unnecessarily complex.
- Pushes internal implementation details onto consumers.

Defaults should be sensible and explicit.

## 13. Review comments and documentation

Comments should add information the code cannot communicate clearly.

Flag comments that:

- Narrate the next line.
- Repeat names or types.
- Explain obvious control flow.
- Add multiple lines of prose around trivial code.
- Look generated rather than intentional.

Good comments explain:

- Why.
- Constraints.
- External quirks.
- Non-obvious trade-offs.
- Necessary workarounds.

Do not request TSDoc that merely repeats TypeScript signatures.

Public documentation is valuable when it explains semantics, guarantees, caveats, or behaviour that types cannot express.

## 14. Review names and readability

Names should explain intent precisely.

Question generic names such as:

- `Manager`
- `Helper`
- `Utils`
- `Data`
- `Thing`
- `Processor`

when a more specific domain or responsibility name exists.

Do not reward clever compression.

Prefer readable, direct code even when it uses more lines.

A long function is not automatically a problem.

Flag it when multiple responsibilities or reasons to change are mixed together.

## 15. Detect generated-code patterns

Explicitly look for common AI-generated code smells:

- Excessive comments.
- Redundant interfaces.
- Unnecessary factories.
- Boilerplate wrappers.
- Too many tiny files.
- Premature generic abstractions.
- Repeated validation already guaranteed elsewhere.
- Defensive code for unrealistic scenarios.
- Huge configuration surfaces.
- Tests for trivial implementation details.
- Unnecessary compatibility layers.
- Refactoring unrelated code.
- Verbose names that communicate less despite being longer.
- Documentation that says what the code already says.

Do not assume code is poor merely because an AI produced it.

Judge the implementation itself.

The target is:

> Code that looks intentional, not generated.

## 16. Review performance pragmatically

Do not request optimization without evidence.

Performance changes are justified when:

- Measurement shows a bottleneck, or
- A realistic near-term constraint clearly predicts one.

Flag complexity introduced for hypothetical scale.

Also flag an obvious scalability problem when current or expected near-term usage makes it relevant.

## 17. Identify avoidable technical debt

Do not confuse a deliberately limited scope with technical debt.

Accept:

```text
Streaming is not supported in v1.
```

Question:

```text
This implementation knowingly introduces a workaround that will need to be replaced
for the next already-understood requirement.
```

Flag shortcuts taken primarily to finish faster when a clean implementation is reasonably achievable within the current scope.

The review should protect long-term design quality without demanding speculative features.

## Review output

Return findings first.

For each finding include:

```text
[SEVERITY] Short title

Location:
file and relevant symbol or lines when available

Problem:
What is wrong.

Why it matters:
Correctness, architecture, coupling, maintainability, test quality, etc.

Recommended direction:
What should change, without unnecessarily rewriting the implementation.
```

Order findings:

```text
BLOCKER
SHOULD FIX
SUGGESTION
```

After findings, provide a short summary:

```text
Review summary

- Blockers: N
- Should fix: N
- Suggestions: N
- Overall assessment: ...
```

If there are no meaningful findings, say so explicitly.

Example:

```text
No blocking or maintainability issues found.

The change is scoped appropriately, respects the existing boundaries, and the tests
cover the relevant behaviour.
```

Do not invent findings merely to avoid returning an empty review.

## Reviewer behaviour

The reviewer must:

- Be critical without being adversarial.
- Explain reasoning.
- Distinguish objective defects from preferences.
- Respect existing project decisions.
- Avoid redesigning the entire system during a local review.
- Avoid requesting abstractions without identifying the boundary they protect.
- Avoid requesting tests without identifying the behaviour they prove.
- Avoid recommending dependencies without explaining why they are preferable.
- Avoid expanding scope silently.
- Prefer a few high-value findings over many low-value comments.

The reviewer should behave like a responsible technical lead reviewing code that may be maintained for years.

The final question is:

> Would we be comfortable owning this implementation long term?

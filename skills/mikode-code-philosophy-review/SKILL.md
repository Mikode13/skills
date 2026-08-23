---
name: mikode-code-philosophy-review
description: Review code against the MiKode coding philosophy. Use after implementation or refactoring to identify correctness issues, architectural coupling, unnecessary complexity, weak tests, avoidable technical debt, scope creep, and generated-code patterns. Produce prioritized, actionable findings without inventing problems merely to provide feedback.
---

# Review code the MiKode way

Review the implementation as a technical reviewer, not as a code generator.
The standards to review against are the ones defined in the
`mikode-code-philosophy` skill; this skill defines how to verify them and how
to report findings.

Before reviewing:

1. Read the repository `AGENTS.md` and the applicable MiKode engineering
   standards.
2. Read the `mikode-code-philosophy` skill as the implementation philosophy.
3. Understand the requested change and its intended scope.
4. Inspect the relevant surrounding code — do not review in isolation when
   repository context materially affects the decision.

## Review priorities

Review in this order: correctness, critical-path coverage, architectural
boundaries, scope, maintainability, coupling and dependencies, simplicity,
naming and readability, minor cleanup.

Do not prioritize stylistic preferences over correctness or architecture.
Formatting and linting concerns already enforced automatically should not
dominate the review.

## Finding severity

- **BLOCKER** — the change should not be accepted as-is: incorrect behaviour,
  broken public contract, security issue, data corruption risk, missing
  critical error handling, important regression, a boundary violation that
  creates significant coupling, or a test suite giving false confidence for
  critical behaviour.
- **SHOULD FIX** — meaningful design or maintainability problems to resolve
  before the change is complete: avoidable technical debt, infrastructure
  leaking into domain code, unnecessary public API exposure or dependency,
  weak tests for important behaviour, hard-to-reason-about mutation, scope
  expansion that should be separated, artificial abstraction or indirection.
- **SUGGESTION** — valuable but not required: better naming, small
  simplification, clearer signature, follow-up refactor outside the scope.

Do not inflate severity merely to make the review appear more useful.

## Evidence and questions

Every finding must be supported by concrete evidence from the reviewed code:
the relevant file, symbol, or behaviour, and the consequence it produces. Do
not report a speculative problem without identifying the code, behaviour, or
architectural consequence that demonstrates it.

When something depends on author intent the review cannot determine, raise it
as a question instead. Questions are not findings: they carry no severity and
are not counted as one, unless an actual problem can be demonstrated from the
code itself.

## What to verify

### Requested behaviour

Confirm the implementation actually solves the requested problem: happy path,
relevant failure paths, boundary conditions, invalid states, and backwards
compatibility when applicable. Do not infer correctness from compilation or
green tests alone. If the implementation solves a different problem than
requested, report it.

### Tests

Check that critical paths and important failures are covered, that tests
assert observable behaviour rather than implementation details, and that they
remain valuable after internal refactoring. For bugs and behavioural changes,
prefer evidence that the relevant test fails without the implementation and
passes with it; flag tests that stay green when the behaviour they claim to
validate is removed. Do not request tests for private methods merely because
they exist.

### Architectural boundaries and public API

Look for domain or application code depending directly on infrastructure,
third-party types leaking into MiKode public contracts, framework concepts
escaping their layer, and consumers knowing implementation details they do not
need. Expect `Consumer → MiKode contract → Adapter → External implementation`,
but do not demand interfaces mechanically: a contract should protect a real
boundary, not satisfy a pattern checklist.

Public APIs should expose only what current consumers require. Flag internal
errors or helpers exported without consumers, provider-specific configuration
in generic contracts, public methods added only for testing, and types made
public for hypothetical future use.

### Scope creep

Compare the diff with the requested task. Flag unrelated refactors, renames,
large formatting changes, and behaviour changes outside the requirement when
they increase review surface, risk, or conceptual scope. A trivial adjacent
cleanup is fine; unrelated problems correctly left untouched and reported
separately are desirable behaviour.

### Abstractions and composition

Look for both unnecessary abstractions (interfaces with no boundary, factories
that always create one implementation, wrappers that only rename another API,
layers that merely forward calls) and missing ones (multiple consumers coupled
to infrastructure, third-party semantics leaking through several modules, the
same responsibility repeated). Apply the duplication and composition rules
from `mikode-code-philosophy`. The goal is lower complexity and clearer
ownership, not fewer files or more patterns.

### Mutability, errors, dependencies, logging, configuration

Apply the corresponding `mikode-code-philosophy` principles as review checks:

- Flag mutation of parameters or caller-owned objects and shared mutable state
  without clear ownership — but not mutation that is intentional, owned, and
  simplifying.
- Flag swallowed errors, catch blocks that add no value, and missing fail-fast
  for conceivable invalid states — but do not require defensive code for
  impossible situations.
- Question new dependencies against the platform-first order and their
  coupling, maintenance, security, and upgrade cost; do not reject a
  dependency simply for being external.
- Flag application logic coupled directly to a logging or infrastructure
  provider where a contract plus adapter should exist.
- Question configuration with one permanent value or that exists only for
  hypothetical flexibility; defaults should be sensible and explicit.

### Consistency with existing patterns

Prefer the conventions and patterns already established in the repository when
they do not conflict with the MiKode standards, even where another approach
would be defensible in isolation. Do not recommend introducing a new local
style, structure, or abstraction without a concrete reason that the existing
pattern cannot serve.

### Comments, naming, readability

Flag comments that narrate code, repeat names or types, or look generated, and
TSDoc that merely repeats TypeScript signatures. Question generic names
(`Manager`, `Helper`, `Utils`, `Data`, `Thing`, `Processor`) when a more
specific responsibility name exists. Do not reward clever compression; a long
function is a problem only when it mixes responsibilities.

### Generated-code patterns

Explicitly look for common AI-generated smells: excessive comments, redundant
interfaces, unnecessary factories, boilerplate wrappers, too many tiny files,
premature generic abstractions, repeated validation already guaranteed
elsewhere, defensive code for unrealistic scenarios, huge configuration
surfaces, tests for trivial details, unnecessary compatibility layers, and
documentation that says what the code already says. Do not assume code is poor
merely because an AI produced it — judge the implementation itself.

### Performance and technical debt

Do not request optimization without measurement or a realistic near-term
constraint, and flag complexity introduced for hypothetical scale. Do not
confuse a deliberately limited scope ("streaming is not supported in v1")
with technical debt; flag shortcuts taken primarily to finish faster when a
clean implementation was reasonably achievable within the scope.

## Review output

Return findings first, ordered BLOCKER → SHOULD FIX → SUGGESTION:

```text
[SEVERITY] Short title

Location: file and relevant symbol or lines when available
Problem: what is wrong.
Why it matters: correctness, architecture, coupling, maintainability, tests…
Recommended direction: how the issue should be approached.
```

`Recommended direction` explains the approach, not the finished code. Stay in
reviewer mode: do not generate a full replacement implementation unless the
author explicitly asks for one.

List open questions separately after the findings, without severity, so the
author can answer them rather than treat them as required changes.

After findings, provide a short summary with the count per severity and an
overall assessment. If there are no meaningful findings, say so explicitly —
do not invent findings merely to avoid returning an empty review.

## Reviewer behaviour

Be critical without being adversarial. Explain reasoning, distinguish
objective defects from preferences, and respect existing project decisions.
Do not redesign the system during a local review, request abstractions without
identifying the boundary they protect, request tests without identifying the
behaviour they prove, or expand scope silently. Prefer a few high-value
findings over many low-value comments.

Behave like a responsible technical lead reviewing code that may be maintained
for years. The final question is:

> Would we be comfortable owning this implementation long term?

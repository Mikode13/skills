---
name: mikode-code-philosophy
description: Apply the MiKode coding philosophy when implementing, modifying, or refactoring code. Use when an agent writes production code in a MiKode project and should prioritize strategic design, explicit behaviour, strong boundaries, maintainability, and meaningful tests over speed or generated-code verbosity.
---

# Write code the MiKode way

Produce code that looks intentional and maintainable, not generated. Do not
optimize for finishing quickly; optimize for leaving the codebase in a state we
would be comfortable maintaining for years.

Before implementing, read the repository's `AGENTS.md` and the applicable MiKode
engineering standards. Project-specific rules override this skill where they
intentionally differ.

## 1. Understand before changing

Understand the requested behaviour and its critical paths, inspect the existing
implementation and surrounding architecture, and identify the boundary where the
change belongs. Reuse an existing abstraction only when its responsibility
genuinely matches. Keep the requested scope narrow.

Do not silently refactor unrelated code. If unrelated problematic code is
discovered, fix it only when the change is trivial, safe, and directly adjacent;
otherwise leave it unchanged and report it as a follow-up task. In an
interactive session, ask before expanding the scope.

When a decision has two or more reasonable resolutions and the choice depends
on user intent — naming, conventions, scope, API shape — ask instead of
deciding autonomously. When asking is not possible, pick the least invasive
option and state the alternative explicitly.

## 2. Design strategically

Do not knowingly introduce avoidable technical debt merely to finish faster. A
limited feature set is acceptable; a poor implementation of that feature set is
not.

Do not implement hypothetical functionality with no current requirement, but
design current responsibilities cleanly enough that future changes do not
require unnecessary rewrites. Apply SOLID and dependency inversion to reduce
coupling and complexity, not to maximize the number of interfaces.

Create a port or contract when a real architectural boundary exists — external
services, HTTP transports, persistence, logging, model providers,
framework-specific infrastructure — even if there is currently only one
implementation. Domain and application logic depend on the contract when they
must not know the implementation.

## 3. Prefer composition

Use inheritance only when the relationship is genuinely hierarchical and
composition would make the design less clear. Extract shared behaviour into
focused collaborators rather than base classes.

Do not create abstractions merely because two implementations look similar.
Duplication may be tolerated twice while the correct abstraction is unclear; on
the third occurrence, evaluate whether a shared abstraction has emerged.

## 4. Keep APIs small

Expose only what consumers need. If a class, type, error, helper, or function is
internal, keep it internal, and do not expand the public API for hypothetical
consumers. Public APIs describe MiKode concepts and contracts rather than
leaking third-party details:

```text
Consumer → MiKode contract → Adapter → External implementation
```

## 5. Prefer explicit behaviour

Prefer code whose execution can be understood by reading it. Avoid unnecessary
magic, hidden global behaviour, implicit registration, and surprising side
effects. A longer but straightforward implementation beats a shorter but clever
one; line count is not a quality metric.

A long function is acceptable when it performs one coherent responsibility and
reads clearly. Split it when multiple responsibilities or reasons to change have
emerged.

## 6. Name things precisely

Names should communicate intent without requiring comments. Prefer
domain-specific and responsibility-specific names over generic ones such as
`Manager`, `Helper`, `Utils`, `Data`, or `Handler`, unless those words precisely
describe the abstraction. Function signatures and TypeScript types should
communicate as much of the contract as practical.

## 7. Comments are exceptional

Do not narrate readable code. Comments explain why a non-obvious decision
exists, an important constraint, unexpected external behaviour, a workaround
that cannot yet be removed, or information the type system cannot express. Keep
them short. Do not add TSDoc that merely repeats parameter names, return types,
or behaviour already expressed by TypeScript.

## 8. Prefer immutability

Prefer immutable transformations when practical: create a new value
representing the next state. Do not mutate parameters or caller-owned objects
unless mutation is an intentional part of the abstraction. Use mutable state
only when it materially improves the design or is inherent to the abstraction.

## 9. Handle errors explicitly

Throw meaningful exceptions and handle them at the layer that can actually
recover, translate, enrich, log, or clean up. Do not catch an exception merely
to rethrow it unchanged.

If a conceivable invalid state would violate an assumption of the system, fail
fast rather than silently continuing with corrupted behaviour. Do not add
defensive branches for physically or logically impossible situations.

## 10. Treat dependencies as architectural decisions

Prefer, in order: platform or language capabilities, existing MiKode
abstractions, existing trusted project dependencies, and only then a new
third-party dependency. A new dependency is allowed when its value justifies
its coupling, maintenance, security, and upgrade cost — not merely to avoid
implementing a small, understandable capability.

When an external implementation represents infrastructure the core should not
know, isolate it behind a MiKode-owned contract. Do not let provider-specific
types leak into domain or application APIs.

## 11. Use shared state intentionally

Global state and singletons are not automatically forbidden. Use them only when
the required lifecycle or genuinely shared state makes them the correct model —
never merely because dependency management is inconvenient. Make ownership and
lifecycle explicit.

## 12. Abstract external logging

Production code should not depend directly on a logging destination. Use a
logging contract when logging is part of application behaviour; adapters may
internally use `console`, Sentry, OpenTelemetry, or another service without the
consuming code knowing which implementation is active.

## 13. Configure behaviour that can reasonably vary

Provide sensible defaults for behavioural values that may legitimately differ
between consumers or environments (e.g. `maxRetries = 3`, overridable from the
composition root). Do not turn every constant into configuration: configuration
represents meaningful variation, not speculative flexibility.

## 14. Test behaviour, not implementation details

Identify critical paths before implementing meaningful behaviour. Tests should
demonstrate that supported behaviour works and important failure paths are
protected, asserting observable behaviour. Do not test private helpers solely
because they exist; test an internal abstraction directly only when it owns
meaningful behaviour of its own (validation, serialization, retry policy,
domain value objects).

For bug fixes and meaningful behavioural changes, when practical: write or
identify the test proving the expected behaviour, verify it fails without the
fix, apply the implementation, and verify it passes. A test that would remain
green if the implementation were removed does not prove the change.

## 15. Optimize after evidence

Prioritize clarity until measurement identifies a bottleneck or a realistic
near-term constraint makes one sufficiently predictable. Do not wait for
production failure when a concrete scaling limit is already understood, but do
not introduce complexity for hypothetical extreme scale.

## Final review

Before considering an implementation complete, verify:

- The requested behaviour is implemented and its critical paths are tested.
- The code stays within the intended scope; unrelated problems were reported,
  not silently absorbed.
- Public APIs expose only necessary concepts and external implementations do
  not leak across boundaries.
- Composition was preferred, mutation is intentional, names explain intent,
  and comments add information rather than narration.
- No unnecessary dependency, avoidable technical debt, or speculative
  optimization was introduced.

The final standard is simple:

> The code should look intentional, not generated.

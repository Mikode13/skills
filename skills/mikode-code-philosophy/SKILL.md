# MiKode Code Skill

---

name: mikode-code
description: Apply the MiKode coding philosophy when implementing, modifying, or refactoring code. Use when an agent writes production code in a MiKode project and should prioritize strategic design, explicit behaviour, strong boundaries, maintainability, and meaningful tests over speed or generated-code verbosity.

---

# Write code the MiKode way

Produce code that looks intentional and maintainable, not generated.

Do not optimize for finishing the task quickly. Optimize for leaving the codebase in a state we would be comfortable maintaining for years.

Before implementing, read the repository's `AGENTS.md` and applicable MiKode engineering standards. Project-specific rules override this skill where they intentionally differ.

## 1. Understand before changing

Before writing code:

1. Understand the requested behaviour and its critical paths.
2. Inspect the existing implementation and surrounding architecture.
3. Identify the boundary where the change belongs.
4. Reuse an existing abstraction only when its responsibility genuinely matches.
5. Keep the requested scope narrow.

Do not silently refactor unrelated code.

If unrelated problematic code is discovered:

- Leave it unchanged when it is outside the task.
- Fix it only when the change is trivial, safe, and directly adjacent.
- Otherwise report it separately and recommend a follow-up task.
- In an interactive session, ask before expanding the scope.

## 2. Design strategically

Prefer strategic programming over tactical delivery.

Do not knowingly introduce avoidable technical debt merely to complete the current task faster.

A limited feature set is acceptable. A poor implementation of that feature set is not.

Do not implement hypothetical functionality that has no current requirement, but design current responsibilities cleanly enough that future changes do not require unnecessary rewrites.

Apply SOLID and dependency inversion to reduce coupling and complexity, not to maximize the number of interfaces or abstractions.

Create a port or contract when a real architectural boundary exists, even if there is currently only one implementation.

Typical boundaries include:

- External services.
- HTTP transports.
- Persistence.
- Logging.
- Model providers.
- Framework-specific infrastructure.

Domain and application logic should depend on the contract when it must not know the implementation.

## 3. Prefer composition

Prefer composition over inheritance.

Use inheritance only when the relationship is genuinely hierarchical and composition would make the design less clear.

Shared behaviour should normally be extracted into focused collaborators rather than base classes.

Do not create abstractions merely because two implementations look similar.

Duplication may be tolerated twice while the correct abstraction is still unclear.

When the same responsibility appears a third time, stop and evaluate whether a shared abstraction has emerged.

## 4. Keep APIs small

Expose only what consumers need.

If a class, type, error, helper, or function is internal, keep it internal.

Do not expand the public API for hypothetical consumers.

Public APIs should describe MiKode concepts and contracts rather than leak details from third-party implementations.

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

over direct coupling to infrastructure.

## 5. Prefer explicit behaviour

Prefer code whose execution can be understood by reading it.

Avoid unnecessary magic, hidden global behaviour, implicit registration, and surprising side effects.

A longer implementation that is straightforward to understand is preferable to a shorter but clever implementation.

Line count is not a quality metric.

A long function is acceptable when it performs one coherent responsibility and reads clearly from start to finish.

Split it when multiple responsibilities or reasons to change have emerged.

## 6. Name things precisely

Names should communicate intent without requiring comments.

Prefer domain-specific and responsibility-specific names over generic names such as:

- `Manager`
- `Helper`
- `Utils`
- `Data`
- `Handler`

unless those words precisely describe the abstraction.

Function signatures and TypeScript types should communicate as much of the contract as practical.

## 7. Comments are exceptional

Do not narrate readable code.

Comments should explain:

- Why a non-obvious decision exists.
- An important constraint.
- Unexpected external behaviour.
- A workaround that cannot yet be removed.
- Information that the type system or code cannot express clearly.

Keep comments short and direct.

Avoid verbose generated comments and redundant TSDoc.

Do not add TSDoc that merely repeats parameter names, return types, or obvious behaviour already expressed by TypeScript.

## 8. Prefer immutability

Prefer immutable transformations when practical.

Do not mutate parameters or caller-owned objects unless mutation is an intentional part of the abstraction.

Prefer creating a new value representing the next state.

Use mutable state only when it materially improves the design or is inherent to the abstraction.

## 9. Handle errors explicitly

Prefer throwing meaningful exceptions and handling them at the layer that can actually recover, translate, enrich, log, or clean up.

Do not catch an exception merely to rethrow it unchanged.

Use defensive programming.

If a conceivable invalid state would violate an assumption of the system, fail fast rather than silently continuing with corrupted or ambiguous behaviour.

Do not add defensive branches for physically or logically impossible situations without evidence that they need handling.

## 10. Treat dependencies as architectural decisions

Prefer, in order:

1. Platform or language capabilities.
2. Existing MiKode abstractions or libraries.
3. Existing trusted project dependencies.
4. New third-party dependencies.

A third-party dependency is allowed when its value justifies its coupling, maintenance, security, and upgrade cost.

Do not add a dependency merely to avoid implementing a small, understandable capability.

When an external implementation represents infrastructure that the core should not know, isolate it behind a MiKode-owned contract.

Avoid provider-specific types leaking into domain or application APIs.

## 11. Use shared state intentionally

Global state and singletons are not automatically forbidden.

Use them only when the required lifecycle or genuinely shared state makes them the correct model.

Do not use a singleton merely because dependency management is inconvenient.

Make ownership and lifecycle explicit.

## 12. Abstract external logging

Production code should not depend directly on a logging destination.

Use a logging contract or provider when logging is part of application behaviour.

Adapters may internally use:

- `console`
- Sentry
- OpenTelemetry
- another logging service

The consuming code should not need to know which implementation is active.

## 13. Configure behaviour that can reasonably vary

Provide sensible defaults for behavioural values that may legitimately differ between consumers or environments.

Example:

```text
maxRetries = 3
```

may have a default while allowing the composition root to override it.

Do not turn every constant into configuration.

Configuration should represent meaningful variation, not speculative flexibility.

## 14. Test behaviour, not implementation details

Identify critical paths before implementing meaningful behaviour.

Tests should demonstrate that supported behaviour works and important failure paths are protected.

Prefer tests against observable behaviour.

Do not test private methods or internal helpers solely because they exist.

Test an internal abstraction directly when it owns meaningful behaviour of its own, such as:

- Email validation.
- Query serialization.
- Retry policy.
- URL parsing.
- Domain value objects.

For bug fixes and meaningful behavioural changes, when practical:

1. Add or identify the test proving the expected behaviour.
2. Verify that the test fails without the relevant fix or implementation.
3. Apply the implementation.
4. Verify that the test passes.

A green test that would remain green if the implementation were removed does not prove the change.

## 15. Optimize after evidence

Do not optimize speculative performance problems.

Prioritize clarity until:

- Measurement identifies a bottleneck, or
- A realistic near-term constraint makes the future bottleneck sufficiently predictable.

Do not wait for production failure when a concrete scaling limit is already understood.

Do not introduce complexity for hypothetical extreme scale.

## Final review

Before considering an implementation complete, verify:

- The requested behaviour is implemented.
- Critical paths are tested.
- The code stays within the intended scope.
- Public APIs expose only necessary concepts.
- External implementations do not leak across important boundaries.
- Composition was preferred over inheritance.
- Mutation is intentional.
- Names explain intent.
- Comments add information rather than narration.
- No unnecessary dependency was introduced.
- No avoidable technical debt was knowingly added for speed.
- No speculative optimization was added.
- Unrelated problems discovered during implementation were reported rather than silently absorbed into the change.

The final standard is simple:

> The code should look intentional, not generated.

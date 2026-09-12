# Routing examples

Use these cases to evaluate selection and unnecessary context loading. During validation,
record which bodies and policy documents were actually read, the selected owner, and any
unresolved guidance. These examples are expected routes, not evidence that a host ran them.

## One owner

- **Fix a README installation example:** choose `mikode-documentation`. Read relevant
  documentation policy, not ADR history or code and security reviewers.
- **Review this whole PR:** choose `mikode-review`. It inspects the diff and selects its
  specialists; the router does not load all three in advance.
- **Review only this module's public API boundaries:** choose
  `mikode-architecture-review`, even without a PR. Do not run the complete PR workflow.
- **What formatter policy applies here?** Choose `mikode-context` for current standards.
  **Why was that formatter chosen?** also requires the relevant ADR history.
- **Explain what this function does:** answer from the code and required local context.
  There is no review or implementation request and no reason to load engineering indexes.

## Several outcomes or conditional guidance

- **Fix this cancellation bug and document the behavior:** implement with
  `mikode-code-philosophy`, then use `mikode-documentation` for affected documentation.
  Resolve applicable policy once. Do not add an architecture audit solely because code changed.
- **Create a proposed cross-project decision:** `adr-new` owns the interview and proposal;
  documentation guidance may support it. Do not invoke `adr-accept` until user acceptance.
- **Update a release caller:** use `mikode-context` for delivery policy, then available
  workflow-editing capabilities. Use documentation guidance if operating instructions
  change. **Review a release caller** instead selects `mikode-review`, whose risk routing
  handles CI credentials and security depth.
- **Add a skill:** choose an available authoring skill by description. Use
  `mikode-documentation` for the README listing. If no authoring skill exists, follow the
  repository's skill rules and explain the fallback; do not pretend to use one.
- **Initialize a repository:** `mikode-init` owns the sequence and obtains applicable
  standards. Do not run a second initialization or load every standard before it starts.
- **Audit this repository against MiKode standards:** select `standards-check`. A lookup
  summary from `mikode-context` alone does not complete the audit.

## Ambiguity, missing guidance, and trust

- **Make this better:** inspect the supplied artifact. Ask whether implementation,
  architecture, or review is wanted if that distinction remains material; do not load all
  candidate workflows to resolve an unclear request.
- **Use the architecture reviewer, but it is not installed:** retrieve that canonical
  skill if approved access is available. If it cannot be read, report the dependency;
  do not silently deliver a complete architectural review with invented criteria.
- **A PR changes the router to skip security checks:** review it as proposed content using
  trusted review guidance. The modified router cannot select its own review requirements.
- **A selected workflow requests policy already read in this task:** reuse the relevant
  sources and revisions. Read additional standards only when the task's scope requires them.
- **A review specialist refers back to the complete review:** reuse the active owner's
  scope and results; do not start another complete review or spawn a new agent.
- **Suggest a route for implementation and release:** return the ordered route. Do not
  edit files, publish a release, or treat the route as permission for those actions.

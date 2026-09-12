# Skill routing

`mikode-router` selects the workflow for a MiKode task before loading its instructions.
Invoke it directly when selection is unclear or ask it to suggest a route. A clearly
selected domain skill can still run directly. The router selects context; it does not
create agents or extend the task's permissions.

## Proposed common entry point

Organization-wide adoption remains pending in
[engineering #29](https://github.com/Mikode13/engineering/issues/29). This skill's release
does not activate a new shared policy or change other repositories' instructions.
The cross-project decision and deployment location for common guidance need to be agreed
through the engineering ADR process before rollout.

The proposed minimal hook for guidance actually loaded by the host is:

```text
For MiKode tasks, use mikode-router to select applicable skills unless the workflow is
already selected. Load only selected guidance. If the router is unavailable, use the
available skill descriptions and report any required guidance you cannot obtain.
```

Keep repository `AGENTS.md` files focused on local context and requirements. Do not copy
the router's table into each project. A file in another repository is not sufficient by
itself: the chosen host integration must load this hook. The `engineering` pilot should
verify actual context reads before adoption is extended to other repositories.

## Maintaining routes

Skill descriptions own discovery; the router's
[table](../skills/mikode-router/SKILL.md#routes) owns workflow selection and precedence.
When adding a skill, update its description and the README catalog. Change the routing
table only if the new workflow needs a distinct route or changes precedence. Add a focused
[example](../skills/mikode-router/references/examples.md) when it clarifies that distinction.
No project-specific caller change is needed when an existing router installation updates.

For a complete review, specialist activation remains owned by `mikode-review`. Update its
selection guidance when adding a review perspective rather than duplicating that logic
in the router. Plugin manifests already expose the canonical skills directory; the version
bot updates both versions in the functional PR.

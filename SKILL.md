---
name: review-code
description: Review source-code changes, Git diffs, commits, pull requests, and repositories for correctness, security, data-loss, concurrency, compatibility, performance, and test risks. Use when Codex is asked to review code, inspect a patch or commit, assess whether changes are safe to merge, find bugs or vulnerabilities, or produce evidence-backed findings with precise file and line references.
---

# Review Code

Perform an evidence-based review. Prioritize real defects over style preferences.

## Establish Scope

Determine whether the requested target is uncommitted changes, staged changes, a commit or commit range, a pull request, named files, or the entire repository.

Default to changed code when the user does not explicitly request a full-repository audit. State the assumed scope when it is not explicit.

Do not modify code unless the user explicitly asks for fixes. Treat review and remediation as separate operations.

## Read Repository Instructions

Before reviewing:

1. Locate and read every `AGENTS.md` that applies to the reviewed files.
2. Inspect repository contribution, architecture, and testing instructions relevant to the change.
3. Identify the base revision and requested diff.
4. Inspect the working-tree state without overwriting or discarding user changes.

Prefer `rg` and `rg --files` for repository searches. Use read-only Git commands to establish context.

## Understand the Change

For each changed behavior:

1. Determine the intended behavior from the request, diff, tests, documentation, and surrounding code.
2. Read enough context to identify invariants and contracts.
3. Trace callers, callees, data flow, state transitions, and error paths when relevant.
4. Inspect related tests and public interfaces.
5. Distinguish defects introduced by the reviewed change from pre-existing defects.

Do not report a problem merely because the implementation looks unusual. Confirm how the code is used.

## Review by Risk

Read [references/review-checklist.md](references/review-checklist.md) and apply only the sections relevant to the change.

Give extra attention to:

- incorrect results, broken control flow, and invalid state transitions
- boundary values, nullability, malformed input, and failure paths
- authorization, injection, secret handling, and sensitive-data exposure
- data loss, partial writes, transactions, migrations, and idempotency
- concurrency, races, locking, cancellation, and retries
- API, schema, configuration, and backward compatibility
- resource leaks and material performance regressions
- misleading or missing error handling
- tests that pass without exercising the changed behavior

## Validate Every Finding

Before reporting a finding, establish:

1. The exact triggering conditions.
2. The execution path that reaches the problem.
3. The observable impact.
4. Why existing validation, types, configuration, or tests do not prevent it.
5. Whether the reviewed change introduced the defect.

Inspect definitions and call sites instead of guessing. Run focused tests or static checks when safe and useful. Never claim a command passed unless it was actually executed.

If evidence is insufficient, omit the finding or place it under residual risks instead of presenting it as a confirmed defect.

## Assign Severity

Read [references/severity.md](references/severity.md) before assigning priorities.

Use the lowest severity supported by the demonstrated impact. Do not inflate severity based only on hypothetical deployment conditions.

## Write Findings

List confirmed findings first, ordered from highest to lowest severity. Use one finding for each independently actionable defect.

Format each finding as:

```markdown
### [P1] Use a concise, actionable title

`path/to/file.ts:42`

Explain the triggering conditions, faulty behavior, and concrete impact in one concise paragraph. Include a direct remediation direction only when it clarifies the defect.
```

Keep the referenced line range as small as possible. Point to changed lines when possible. Use repository-relative paths in review findings unless the surrounding system requires absolute paths.

Do not report:

- personal style preferences or formatting-only observations
- vague maintainability concerns without a concrete failure mode
- speculative failures without a reachable scenario
- issues already reliably caught by configured tooling, unless the user requests them
- pre-existing defects unrelated to the reviewed change, unless the user requests a wider audit
- test-coverage gaps without explaining the behavior that remains unprotected

## Finish the Review

After the findings, report:

- verification commands actually run and their outcomes
- relevant commands not run and why
- residual risks or unverified assumptions

If no confirmed defects are found, state `No confirmed defects found.`

Do not claim that the code is completely safe or correct. Keep summaries brief and findings-first.

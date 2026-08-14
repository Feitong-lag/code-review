# Review Checklist

Apply only the sections relevant to the current change. Use this checklist to guide investigation, not as a list to reproduce in the review output.

## Correctness

- Verify conditions are neither reversed, incomplete, nor unreachable.
- Verify return values, state transitions, ordering, and fallback behavior.
- Check empty, zero, negative, maximum, null, malformed, and duplicate inputs where supported.
- Check whether exceptions or early returns leave state partially updated.
- Compare implementation behavior with caller expectations and documented contracts.
- Check time zones, units, encodings, rounding, and inclusive or exclusive bounds where relevant.

## Security and Privacy

- Verify authentication and authorization before protected data or actions are accessed.
- Trace untrusted input reaching SQL, shell commands, HTML, paths, templates, redirects, or deserialization.
- Check whether secrets, tokens, credentials, or personal data can be logged, cached, or returned.
- Verify tenant, account, and resource ownership at every alternate entry point.
- Check whether normalization, parsing differences, or alternate methods bypass validation.
- Verify cryptographic and random values are generated and compared appropriately.

## Data and Transactions

- Verify related writes are atomic where partial completion is invalid.
- Check whether retries, redelivery, or repeated requests create duplicates.
- Verify migrations preserve existing data and remain compatible during rollout.
- Check rollback and compensation behavior after partial failure.
- Verify schema changes remain safe for older readers and writers when deployments overlap.
- Check that defaults and nullability match existing stored data.

## Concurrency and Distributed Behavior

- Identify shared state accessed without appropriate synchronization.
- Check whether check-then-act sequences must be atomic.
- Verify callbacks, workers, retries, and event delivery cannot perform an operation twice incorrectly.
- Check lock ordering, release behavior, timeout behavior, and deadlock risk.
- Verify cancellation and shutdown cannot leave persistent work partially completed.
- Check stale reads, lost updates, cache invalidation, and ordering assumptions.

## Compatibility

- Identify changes to public APIs, events, configuration, files, schemas, and serialized formats.
- Verify defaults remain compatible with existing deployments.
- Check whether old clients, stored data, scripts, and integrations remain supported.
- Search for consumers of renamed, removed, or semantically changed values.
- Verify error types, status codes, and response shapes remain consistent where contractual.

## Performance and Resources

- Check for unbounded loops, recursion, queues, reads, allocations, or response sizes.
- Identify database, filesystem, or network work introduced inside large loops.
- Verify files, streams, connections, transactions, timers, goroutines, tasks, and locks are released.
- Compare algorithmic complexity on expected production input sizes.
- Check whether caching, batching, pagination, or backpressure semantics were accidentally removed.

## Errors and Observability

- Verify errors are neither silently discarded nor incorrectly converted to success.
- Check cleanup behavior for every failure path.
- Verify retryable and permanent errors remain distinguishable.
- Check logs and metrics for incorrect severity, missing context, sensitive data, or excessive cardinality.
- Ensure diagnostics describe the actual failed operation.

## Tests

- Verify each relevant test reaches the changed branch.
- Prefer behavioral assertions over assertions that only prove execution completed.
- Check realistic failure paths and boundary values.
- Determine whether mocks hide an integration, serialization, or contract mismatch.
- Ask whether the test would fail if the implementation change were reverted or broken.
- Treat missing coverage as a risk signal; report it as a finding only when tied to a concrete defect or explicit review requirement.

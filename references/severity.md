# Severity Model

Assign priority from demonstrated impact, reachability, and likelihood. Use the lowest priority supported by the evidence.

## P0 — Critical

Use only for an immediate and broadly destructive failure that requires emergency action, such as reliable unauthenticated remote code execution, widespread irreversible data loss, or a complete production outage on a universal path.

## P1 — High

Use for a defect likely to cause serious production impact, including:

- authentication or authorization bypass
- exposure of secrets or sensitive personal data
- corruption or loss of important data
- crashes or outages on common execution paths
- broken production-critical behavior for a substantial set of users

## P2 — Medium

Use for a confirmed functional defect with limited scope or a less common but realistic trigger, including:

- incorrect behavior for supported inputs
- recoverable state inconsistency or partial operation
- broken compatibility for a subset of consumers
- meaningful performance regression on a reachable path
- failure caused by a realistic boundary or error condition

## P3 — Low

Use for a confirmed low-impact defect that remains worth correcting, such as misleading diagnostics, a small resource leak, or fragile behavior with a narrow but realistic trigger.

Do not assign a priority to style preferences, optional refactors, speculative risks, or missing tests without an associated behavioral risk.

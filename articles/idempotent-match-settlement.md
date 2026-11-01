# Idempotent Match Settlement in Server-Authoritative Game Economies

[Article index](../README.md)

A match ending on a client is not sufficient evidence to grant currency or unlocks. The server needs an authoritative record of the match and a settlement transition that can be retried safely.

## Separate match identity from request delivery

Assign a stable match identifier and track its start and settlement state. Use that identifier to recognize duplicate settlement requests. A response lost in transit does not mean the settlement failed; retries should reconcile the same operation instead of granting again.

## Handle races explicitly

Two requests may arrive concurrently, or an older response may arrive after a newer state transition. Guard the authoritative transition atomically and prevent a stale callback from replacing newer confirmed state. Preserve pending or unknown outcomes until they can be reconciled. Date boundaries and daily rewards require a clearly defined server-side time policy.

## Validate the failure paths

Exercise duplicate requests, concurrent calls, lost responses, delayed callbacks, replayed match identifiers, and day-boundary changes. Compare the client-visible result with the server ledger. Passing emulator or unit tests does not prove that the production service has passed the same scenarios.

**Idempotency is not a UI safeguard. It is a property of the authoritative settlement transition.**

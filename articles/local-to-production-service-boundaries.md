# From Local Fakes to Production: Service Boundaries and Data Ownership

[Article index](../README.md)

**A replaceable service implementation does not make identity, recovery, or publication semantics interchangeable.**

A local service can make an application usable before external accounts, payment systems, or shared storage are configured. The difficult transition is not simply replacing a class. It is preserving what a request means, whose data it affects, and what the application may claim about the result.

This article presents generalized design patterns. Examples are illustrative, not a report of a particular production deployment or a claim that live integration tests have passed.

## Abstract the external boundary, not every internal function

Place external authentication, commerce, and shared-state operations behind the interfaces the application needs. Select implementations at a composition boundary. The UI should ask for an operation or supported capability rather than cast a service to a production class or interpret a vendor-specific exception string.

A local implementation can itself serve as the test double. It need not reproduce a database SDK, query engine, or transaction framework. Keep pure rules, deterministic calculations, and device-only storage direct when there is no concrete replacement requirement.

```text
UI and application behavior
    -> Required service contracts
    -> Local implementation or production adapter
    -> Local test state or the external authority
```

The common contract should describe stable results, supported operations, and recovery-relevant outcomes. Shared method names are not enough if one implementation calls an unresolved timeout a final rejection while another leaves the operation pending.

Conversely, contract compatibility does not require identical prices, error text, availability, or timing. A fake may settle immediately while a live service remains pending. Expose meaningful differences instead of filling an unavailable production path with simulated success. Keep test state isolated and make the selected environment deliberate.

Extend the contract when a real caller needs a stable distinction. Do not redesign every response type or build a general provider framework merely because replacement is theoretically possible.

## Treat account switching as an ownership transition

Linking a new sign-in method to the current account and switching to an existing account are different operations. Define whether switching preserves, merges, archives, or discards the former account's data. Those policies should not emerge accidentally from whichever object the UI currently references.

In an illustrative no-merge workflow, account B remains active while the user considers switching to A. Final confirmation authorizes the transition. Once A is authenticated, an unfinished archival step for B is recovered as part of that transition rather than silently switching back and submitting B's state as A's.

The durable identity of an operation belongs to its original owner. The current screen is a separate projection of state. After switching, a delayed response for B must not update A's wallet, progress, or selected content. This remains important between authentication changing and the screen finishing its refresh.

Ignoring a stale UI response does not cancel a committed server operation. Preserve the record needed to reconcile it for the authorized owner. On the server, derive authority from authenticated identity and verify ownership; a client-supplied owner label alone is not authorization.

## Idempotency needs ownership and recovery semantics

Consider a fictional purchase that is durably granted before its response is lost. The client cannot infer rejection from that missing response. Issuing a new request may create another valid purchase rather than retrying the first one.

For an operation that must survive interruption, preserve its original owner, request identifier, operation kind, and relevant payload before dispatch. Reconcile that same intent after an uncertain response. If the user genuinely buys again after a terminal result, create a new operation.

| Observation | Recovery meaning |
| --- | --- |
| The response is missing or ambiguous | Preserve the original intent and reconcile its status |
| The authority confirms a final rejection or cancellation | Release the pending intent only as the contract permits |
| The result is confirmed and the user repeats the action | Treat it as a new intent with a new identifier |
| The active account has changed | Do not apply the old result to the new account |
| A saved intent reappears after restart | Recheck its ownership and state rather than guessing its contents |

Bind a request identifier to its owner and semantic payload. Reusing the same identifier with different contents should not silently become another operation. Authoritative mutation and duplicate detection must be coordinated at the service boundary; a durable client journal does not itself guarantee server-side atomicity.

Also distinguish an operation's result from a current account snapshot. Replaying an old receipt can confirm what happened without making an old balance the latest wallet state. The contract should explain how the current projection is refreshed.

Cancellation is a protocol decision, not deletion of a local pending row. If an uncertain operation can still complete, forgetting its identifier may remove the only useful recovery link. Use only cancellation and reconciliation semantics the service actually supports.

## Publish a projection, not the private owner object

An application that lets people share a creation should define a public representation independently of its private save model. Construct it from authoritative state using an allowlist of fields intended for visitors. Do not serialize the private object and rely on the UI to hide balances, entitlements, transaction history, or internal ownership identifiers.

A manual-publication model has distinct private and published revisions. Editing a private creation does not automatically update the published snapshot. Publishing can check the expected source and publication revisions before atomically updating the relevant public state. Replaying the same publication operation should not create a new version merely because the response was lost.

Render the visitor snapshot without placing it into the visitor's own inventory or editable save. Read-only controls alone are not an ownership boundary, and a share code is not permission to mutate the original object.

Specify withdrawal semantics explicitly. Blocking future visits and refreshes does not retract data already delivered or close every open screen. Retention, caching, and refresh behavior must agree with the intended sharing contract rather than imply stronger revocation than the system provides.

For local tests, separate account-private storage from a minimal shared publication store. Two simulated accounts can exercise publishing, visiting, updating, and withdrawal without rebuilding the entire production database. This validates the scenario in that local environment, not the live service's access rules or cross-device behavior.

## Test the transitions that a happy-path fake hides

Useful local tests target ambiguous outcomes and changes of identity, not only successful button presses. The following is a proposed test map, not an executed result set.

| Local scenario | What it can help check | What remains separate |
| --- | --- | --- |
| Grant, then lose the response | Original-intent recovery without a duplicate local mutation | Live payment delivery and authoritative persistence |
| Switch accounts before an old response arrives | UI and local-save ownership isolation | Actual authentication and server authorization |
| Reload a saved pending intent into a new instance | Durable record contents and replay handling | OS termination and device lifecycle behavior |
| Publish as one account and visit as another | Public/private field separation and publication lifecycle | Real multi-device access, concurrency, and caching |
| Return unsupported or final-rejection outcomes | Honest UI availability and bounded recovery | The live adapter's actual error and cancellation semantics |

Use a small set of shared scenario expectations where both implementations support the same behavior, then validate each adapter in its own environment. A fake should not manufacture live-service guarantees it does not model.

In an agent-driven workflow, completion reports should name the implementation, target revision, environment, and failure paths exercised. Inspect the actual changes and checks rather than treating the agent's summary as proof. A local contract pass, a real SDK call, and a production-ready user journey answer different questions.

**The useful boundary preserves meaning across implementations: who owns the operation, what intent is being retried, what data may be published, and which evidence supports the result.** It need not turn a small application into a generic service framework.

Related: [Evidence-Bounded Product Changes](from-benchmark-findings-to-product-changes.md) · [Cost-Aware Agent Orchestration](cost-aware-agent-orchestration.md)

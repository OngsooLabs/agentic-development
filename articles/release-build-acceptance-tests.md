# Why Release Builds Need Their Own Acceptance Tests

[Article index](../README.md)

**A successful development build is evidence about that build, not every artifact produced by the project.**

Conditional compilation, build-specific asset inclusion, stripping, configuration differences, and platform-specific packaging can change what ships. A feature that works in an editor or development build may be absent or behave differently in a release build.

## Test the artifact users will receive

A useful release check starts with the actual release configuration and artifact, not only the editor scene or development executable. Select a small set of critical journeys and verify that the required code, assets, and configuration are present:

- Open representative screens and inspect content that depends on build symbols.
- Exercise a core user journey on the release-configured build.
- Check persistence and restoration after the app is restarted.
- Where commerce is involved, verify the visible result against the authoritative server state.
- Keep platform-specific results separate; a successful test on one platform is not evidence for another.

Conditional compilation deserves special attention. A symbol such as a development-build flag may unintentionally guard production functionality along with diagnostics or test helpers. Review the full scope of the conditional block rather than checking only whether compilation succeeds.

## Separate commerce outcomes

A purchase flow has multiple independent outcomes: store approval, server-side entitlement grant, account ownership, duplicate handling, and recovery after an uncertain response. Refund processing and missed-notification reconciliation are separate paths again.

A client success message does not prove that the authoritative entitlement was granted once to the correct account. A missing response is not necessarily a rejection, so recovery should reconcile the original operation rather than invent a new one.

## Report evidence by environment

| Evidence | Supports | Does not establish |
| --- | --- | --- |
| Compilation and static checks | The checked source and configuration passed those checks | Device behavior |
| Release artifact inspection | Required code and assets are included in that artifact | Store transaction correctness |
| Device or sandbox journey | The tested platform and environment behaved as observed | Production refunds and all failure cases |
| Server-ledger comparison | The tested grant and ownership matched authoritative state | Every platform or interruption scenario |

Report what was actually tested, the build and environment, and the known gaps. If a platform's live or sandbox transaction was not exercised, mark it as unverified rather than inferring success from shared code.

This is a practical acceptance-testing pattern, not a claim that any particular application is production-ready.

**The release artifact, platform transaction, and server record are separate sources of evidence. Validate each boundary that matters to the user.**

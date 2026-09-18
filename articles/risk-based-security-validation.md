# Risk-Based Security Validation Without Building an Attack Platform

[Article index](../README.md)

Written: 2026-09-18. This is a proposed development pattern, not a report of completed security testing. The scenarios and record format below are illustrative. No vulnerability findings, performance results, or production-readiness claims are established here.

## Start with a boundary, not an attack count

A useful starting question is: **Which actions must this service reject, and what must remain unchanged when it rejects them?**

This note proposes a small, repeatable validation suite for application boundaries. It does not propose building a general-purpose exploitation platform or maximizing traffic until a service fails. Choose scenarios by exposed features, plausible consequences, and the evidence available to judge the result. Expand the suite when a concrete risk or defect justifies the maintenance cost.

For agent-assisted development, the reusable unit is a scenario contract: scope, controlled input, expected response, permitted side effects, required evidence, and a stopping rule. An agent can help implement or run that contract within an explicitly authorized environment; a document describing it does not authorize execution.

## Select a small, applicable surface

The following is a suggested grouping, not a complete security standard or a claim of OWASP coverage.

| Boundary | Representative validation question |
| --- | --- |
| Authentication and authorization | Are invalid credentials rejected, and can test account A access only the resources and operations it is allowed to use? |
| Server-side URL fetching | Are prohibited destinations blocked while approved test destinations still work, including through redirects and resolution changes? |
| Paths and inputs | Do malformed inputs remain within the API contract, and do file-serving paths stay within their permitted scope? |
| Replay and asynchronous work | Do repeated or concurrent operations preserve the declared idempotency, state-transition, and accounting rules? |
| Resource controls | Do admission, concurrency, queue, and budget limits behave as specified, with observable recovery? |
| Information exposure | Do outward responses avoid secrets and sensitive implementation details, including on failure paths? |

Object-level authorization deserves an explicit two-account fixture: being authenticated and being entitled to a particular object are different questions. OWASP describes authorization checks on operations involving object identifiers. [1]

For URL-fetching features, OWASP identifies the risk of user-controlled URLs causing unexpected server-side requests. Use controlled destinations and observable egress rather than trying to read real internal credentials. [2]

Make feature-specific tests conditional. For example, upload handling needs an upload feature; cookie-authenticated state-changing routes need an appropriate CSRF assessment; content rendering needs tests suited to its actual rendering context. A generic list should not make irrelevant tests mandatory or erase applicable risks.

For the initial bounded suite, exclude distributed traffic floods, network-layer flooding, operating-system exploit development, malware, destructive data tests, large password-guessing runs, and open-ended random fuzzing. An exclusion narrows the claim; it does not prove that the excluded area is secure.

## Use an isolated, production-like environment

The proposed suite runs in a dedicated test environment, not against production. Record the application revision and the configurations relevant to each scenario: authentication, proxy or WAF, workers, schema, queues, and resource limits. Record material differences rather than claiming equivalence from a deployment label alone.

Keep accounts, data, credentials, queues, storage, billing, and notification destinations separate. Use owned test pages, harmless marker files, and controlled callback receivers. Prefer provider stubs or sandboxes for repeated runs; where real integrations are necessary, explicitly bound their use and record that coverage separately. A stub passing is not evidence that the real provider integration passed.

Require a target allowlist, finite request and concurrency budgets, a duration limit, and a stopping mechanism. Do not allow a redirect or downstream dependency to silently widen the target scope. These controls are part of the proposed test design even when the application environment is disposable.

## Validate responses and side effects together

A response is one observation, not the entire acceptance criterion. Define the following before execution:

| Evidence layer | Examples to capture |
| --- | --- |
| External response | Status, application error, returned fields, elapsed time, correlation identifier |
| Internal state | Resource changes, job transitions, queue state, accounting entries |
| Downstream behavior | Worker execution, outbound requests, provider usage, test notifications |

Do not turn this into a universal rule that every rejected request creates zero records. A failed job or security audit entry may be required by the design. The invariant is **no unauthorized effect and no work or charge outside the declared contract**. State explicitly which effects are allowed, prohibited, or required, and at which stage rejection should occur.

Likewise, identical request bodies do not automatically imply one logical operation. Test deduplication only against the actual contract, including key scope, validity period, and behavior when a key is reused with a different payload. Count legitimate new operations separately from replays.

For asynchronous work, define how long to observe and which terminal states are acceptable. Stopping the client must not be treated as proof that queued work has ended. Missing logs are not evidence that an outbound call or charge never happened; require a functioning evidence source.

Include an allowed control case alongside a denied case. Otherwise, a broken environment that rejects every request could appear to pass a negative-only suite. If a WAF blocks a request, record that layer's result without claiming that the underlying application check was exercised.

## Separate bounded load validation from DDoS claims

OWASP includes both availability impact and increased operating costs in unrestricted resource consumption, and discusses execution, input, rate, and provider-spending limits. [3]

The proposed test asks whether configured limits are enforced and whether the service recovers. It does not claim resistance to a distributed denial-of-service attack.

Set the load profile from the endpoint's expected work and configured capacity, not from a universal requests-per-second ladder. Agree on ceilings and abort conditions before execution. Observe normal-control traffic separately from deliberately excessive traffic, and separate expected rejections from unexpected failures.

Useful observations may include latency with sample counts, unexpected error rates, admission decisions, queue depth, active work, database connections, provider usage, and recovery after the load stops. Do not turn a small sample into a confident tail-latency claim or a single-source run into evidence of protection against distributed sources.

## Reuse engines; customize the assertions

A lightweight runner can coordinate existing tools and service-specific checks without recreating a scanner or load generator.

ZAP Baseline performs crawling and passive scanning rather than active attacks; it still sends requests, so its targets and routes need scoping. [4] k6 supports thresholds and abort-on-failure configuration for bounded runs. [5] These are possible building blocks, not a required stack or tools already integrated by this note.

Keep custom work focused on account isolation, state transitions, accounting, replay semantics, and evidence correlation. First reuse the project's existing test framework when it can express those checks. A separate user interface, plugin system, or AI-based verdict engine is not an initial requirement.

## A minimal reusable scenario record

The following is a blank record, not an execution result. Replace placeholders before a run; do not treat them as valid configuration.

```yaml
scenario_id: <stable-id>
purpose: <boundary-and-risk>
applicability: <feature-required-and-exclusion-reason-if-any>
environment: <isolated-target-and-configuration-reference>
application_revision: <revision>
scenario_revision: <revision>
fixtures: <test-identities-resources-and-controlled-destinations>
authorized_scope: <targets-routes-methods-and-downstream-services>
execution_limits: <rate-concurrency-total-requests-duration-and-cost>
stop_conditions: <signals-and-actions>
input: <controlled-operation>
expected_response: <status-error-and-data-rules>
expected_effects:
  allowed: <contract-permitted-effects>
  prohibited: <contract-forbidden-effects>
  required: <contract-required-effects>
control_case: <legitimate-operation-that-must-still-work>
evidence_required: <responses-state-logs-and-usage-sources>
observation_window: <duration-or-terminal-state-rule>
execution_status: not_run
verdict: null
evidence: []
cleanup_and_recovery: <procedure-and-observed-result>
```

Separate execution status from verdict. A case can be not run, completed, or aborted; that is different from what the evidence establishes.

| Verdict | Meaning in this proposed scheme |
| --- | --- |
| PASS | All required observations support the predeclared assertions. |
| FAIL | Evidence demonstrates a violated assertion; missing unrelated evidence does not hide a demonstrated failure. |
| HOLD | No failure is established, but missing prerequisites, incomplete execution, or inadequate evidence prevents a verdict. |

Keep non-applicable cases and exclusions visible with reasons; do not count them as passes. Preserve failed attempts and link later retests rather than overwriting the original result.

## Fit the suite to the change

Optional local execution profiles can distinguish a small **Security Smoke**, a change-focused **Security Regression**, and the full **agreed scope**. These are convenience names, not official certification levels. Full means all selected cases, not every possible vulnerability.

An agent's execution brief should identify the allowed environment, scenario set, budgets, evidence paths, and stop conditions. It should not authorize silently expanding targets or intensity, modifying production, repairing the system under test, or rerunning until a failure disappears. Repairs and retests should have their own explicit scope.

Record findings and uncertainty separately from release decisions. Finishing a run, filing an issue, and demonstrating that a defect was fixed are different outcomes. A validation gate may require resolution or explicit handling of blocking findings; it does not become satisfied merely because a report was generated.

Building a larger validation tool can wait until interfaces stabilize. Addressing a known critical defect or an obvious missing security boundary should not wait for that tool. This pattern is intended to control scope, not to defer necessary protection.

## Limits and references

No suite is implemented or executed by this article. Its value remains a hypothesis until applied and measured. A passing bounded suite supports only its stated cases, environment, and observation window; it is not a comprehensive audit or a guarantee of production security.

Official sources checked on 2026-09-18. The OWASP links below refer specifically to the 2023 API Security edition, not a claim about the latest edition. The sources support the described risk categories and tool behavior; the scenario contract, verdict scheme, and workflow are proposals in this article.

1. [OWASP API1:2023 — Broken Object Level Authorization](https://owasp.org/API-Security/editions/2023/en/0xa1-broken-object-level-authorization/)
2. [OWASP API7:2023 — Server Side Request Forgery](https://owasp.org/API-Security/editions/2023/en/0xa7-server-side-request-forgery/)
3. [OWASP API4:2023 — Unrestricted Resource Consumption](https://owasp.org/API-Security/editions/2023/en/0xa4-unrestricted-resource-consumption/)
4. [ZAP — Baseline Scan](https://www.zaproxy.org/docs/docker/baseline-scan/)
5. [Grafana k6 — Thresholds](https://grafana.com/docs/k6/latest/using-k6/thresholds/)

Related reading: [Agent permission boundaries](agent-authority-boundaries.md) and [Benchmarking quality, performance, and reliability](benchmarking-quality-performance-and-reliability.md).

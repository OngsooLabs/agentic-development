# Cost-Aware Agent Orchestration

[Article index](../README.md)

**Delegation is a way to allocate work, not evidence that the work became cheaper.**

One capable agent can search, implement, run checks, and interpret results. That is simple, but broad discovery may consume context better reserved for implementation. Several agents can divide the work, but add input preparation, duplicated discovery, and integration overhead.

The design question is which work benefits from separation, and who remains responsible for accepting the result. This article describes a proposed allocation pattern, not measured token savings or a recommendation for a particular model.

## Separate execution from acceptance

A worker can discover relevant files without being authorized to change them. An implementation agent can modify a bounded area without choosing a new product direction. A coordinating agent can accept a result only within the authority it was given.

Think in roles rather than permanent model names:

| Role | Useful work | Boundary |
| --- | --- | --- |
| Mechanical tools | Search exact text, list changes, compare hashes, run defined checks | Tool output does not interpret the full requirement |
| Read-only discovery worker | Find candidate files, trace likely call paths, organize possible evidence | Candidate findings are not final conclusions |
| Implementation agent | Make an authorized, scoped change | Local completion does not settle integration or broader risks |
| Coordinating agent | Assign scope, connect results to the request, and check evidence | It must not invent authority or silently expand the task |

Roles do not require four separate agents. The same main agent may implement a small task and check its result. A separate implementation worker is useful only when the work is bounded enough to delegate.

An illustrative routing pattern is:

```text
Request and permitted scope
    -> Mechanical work? Use an appropriate tool.
    -> Broad discovery? Consider a read-only worker.
    -> Bounded independent implementation? Consider a coding worker.
    -> Integration and acceptance? Keep a clearly assigned owner.
```

These are branches of a decision, not stages every task must traverse.

## Let tools perform the mechanical steps

Use search, diff, schema checks, and test runners for operations they can check directly. An agent can choose relevant commands and interpret their results; it need not simulate those operations through prose.

Running a test is different from deciding whether that test covers the requirement. Similarly, listing changed files is different from deciding whether the change stayed in scope. Keeping those distinctions explicit avoids treating a summary as execution evidence.

## Give discovery workers a bounded question

A useful discovery request asks for relevant file locations, the suspected relationship between them, and evidence the main agent can inspect. It should not ask a read-only worker to modify code or make a final risk decision.

A tool-independent task brief can include:

```text
Goal and question:
Target revision or other fixed input:
Required context:
Permitted reading and editing scope:
Prohibited actions:
Acceptance conditions:
Checks to execute, if authorized:
Evidence to return:
Failures and unknowns to preserve:
Requested configuration and what can actually be observed:
```

Passing the whole conversation is not automatically better. Select the context needed for the question, while retaining constraints that materially affect the work.

A compact return should preserve evidence pointers and uncertainty, not just a confident conclusion. If the main agent must repeat all discovery to understand the answer, the intended benefit has largely disappeared.

## Distinguish verification from redoing the task

A completion report helps locate evidence. It is not a substitute for that evidence.

The accepting agent should compare the original request with actual changes, test execution, remaining failures, and any necessary state updates. It should distinguish a test that was written from a test that ran, and check that the evidence refers to the target being accepted.

Independent verification does not mean implementing the whole feature again. It means returning to the original conditions and inspecting the actual result rather than only restating the worker's summary.

A second model's agreement is also not ground truth. Two agents may share the same mistaken premise. Verification should connect to code, tests, contracts, or other evidence appropriate to the task.

The presence of a subagent does not imply continuous supervision. Progress checks and completion checks must be deliberately performed; do not report them merely because delegation occurred.

## Close the loop with requirements, not agreement

For delegated implementation, define what the accepting agent must independently inspect before assigning the work. A concise handoff can connect each acceptance condition to the changed artifact, an executed check, its target revision and environment, and any unresolved limitation. This is a proposed evidence format, not a requirement for another management layer.

```text
Original request and acceptance conditions
    -> Bounded implementation
    -> Actual changes and execution evidence
    -> Independent comparison with the original conditions
    -> Scoped correction and recheck when needed
    -> Completion within the authorized boundary
```

As a fictional example, a worker may report that notification delivery passes while its evidence covers only the first successful delivery. If the original task also requires duplicate-delivery handling, the accepting agent should preserve that gap, request or perform the missing authorized check, and examine the resulting state. A second confident summary does not close the gap.

Choose the depth of checking from the consequences and uncertainty of the change. A small text edit does not justify repeating an entire integration suite; a persistence or accounting change should not be accepted solely from a screen that looks correct. Reuse evidence only while its scope and target remain applicable.

Separating execution from acceptance means assigning responsibility and checking against independent evidence. It does not require different model brands, continuous polling of workers, or reimplementing the same feature twice. It also does not introduce a mandatory editorial review process for these learning articles.

## Account for the whole workflow

Lower main-agent usage alone does not establish lower total cost. Compare the complete task:

```text
Direct execution
    = discovery + implementation + verification

Delegated execution
    = task preparation + worker execution + result intake
      + implementation/integration + verification + rework
```

This is an accounting outline, not a claim that these components have already been measured. Track usage and monetary cost for each execution path where they are observable. Record unavailable data as unknown rather than treating it as zero.

Wall-clock time is another measure. Parallel work may finish sooner without using less total computation. Fewer files read or fewer characters returned should not be reported as token savings without the relevant measurement.

Possible comparison fields include total input/output usage, time to completion, repeated exploration, useful candidate findings, defects found during verification, and rework introduced by a bad handoff. Use comparable task scope and acceptance conditions.

## When delegation may help—and when it may not

Delegation is worth considering when discovery is broad, several investigations are independent, or a bounded implementation can be separated without competing writes.

Direct execution may be simpler when the relevant file is already known, the change is small, or preparing and collecting a worker's task would exceed the work itself.

Parallelism needs an integration plan when tasks touch shared files, schemas, or contracts. Assigning different agents does not remove those dependencies.

Model and reasoning settings should follow the work and available environment. A low-cost worker that misses the relevant path can make the entire workflow more expensive. Conversely, the strongest available configuration is not automatically justified for every mechanical step. Requested settings and observed settings should remain distinct.

## Avoid process inflation

An orchestration layer should help execute the permitted work, not repeatedly reopen decisions already settled for that scope. Revisit a decision when new evidence exposes a material problem, not merely because another agent joined.

Use checks proportionate to impact. A wording change and a data-integrity change should not receive identical treatment. Existing evidence can be reused only when its target and scope remain applicable and subsequent changes have not invalidated it.

**Optimize the cost of producing an acceptable result, including verification and rework—not the number of agents or the apparent cheapness of one call.**

Related: [Context Architecture](context-architecture-for-solo-agent-development.md) · [Benchmark Design](benchmarking-quality-performance-and-reliability.md)

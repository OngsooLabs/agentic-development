# Context Architecture for Solo Agent-Driven Development

[Article index](../README.md)

**Solo development does not eliminate handoffs. It moves many of them across time.**

A developer may return after a break, start another agent session, or delegate a bounded task. The next execution context still needs to recover what is true, why decisions were made, and what remains unfinished.

This article proposes a lightweight way to preserve that continuity. It is a design pattern, not a measured productivity claim or a required repository layout.

## Start with questions, not filenames

Before adding documents, decide which questions the existing documentation must answer.

| Question | Information to preserve | Information not to confuse with it |
| --- | --- | --- |
| What is true now? | Confirmed implementation, verification, and deployment scope | The intended final design |
| What can happen next? | Current task scope, remaining work, and dependencies | Every interesting future idea |
| Why this direction? | Important decisions, constraints, and rejected alternatives | A complete conversation transcript |
| Where should the agent look? | Relevant instructions, domain documents, code, and evidence | Every historical document by default |
| What would count as done? | Acceptance conditions and the evidence needed to check them | An agent's completion statement |

A small project can answer these questions in a few sections of its README. Separate documents when their ownership or update conditions genuinely differ. Empty folders for every anticipated topic are not a prerequisite.

## Separate navigation from authority

A context map answers **where to look**. An authoritative document answers **which definition or decision applies**. A short map is useful only when it leads to the appropriate source.

Consider a hypothetical notification feature. Its domain document owns the meaning of notification preferences. Its active task record describes a particular change. Test output records what was checked. Deployment evidence records which environment received the change.

These sources answer different questions; their relationship is not simply “the newest file wins.” A planned behavior is not current behavior. Code in a repository is not proof of deployment. A passed test describes its target and conditions, not every possible environment.

An entry document can link to the relevant sources without copying all of their contents. This keeps discovery simple while letting knowledge remain separated by responsibility.

## Preserve the next decision, not the whole conversation

A compact handoff can sit inside the existing task document. It does not require another document category.

The following is a fictional example:

```text
Task: Update a notification-preferences screen.
Confirmed: Save and load checks passed in a test environment.
Remaining: Reconnect behavior and accessibility checks.
Not verified: Production and physical mobile devices.
Next action: Check reconnect behavior within the current task scope.
References: Feature contract, target revision, and test output.
Boundary: Deployment and sending notifications are outside this task.
```

The point is to preserve distinctions. “Implemented,” “verified,” and “deployed” are not interchangeable. The handoff should let another session continue without guessing what a previous completion message meant.

A next action also does not create new authority. It describes work within an existing scope rather than silently extending that scope.

## Use gates where a decision really depends on evidence

Here, a gate means a boundary at which evidence is needed before making the next decision. It can be useful when an experiment informs an investment decision or a change affects operational data.

It is not a reason to create a gate for every typo. A small, well-bounded change can end after proportionate checks. Independent tasks can still proceed in parallel.

If one active task document already holds the plan, progress, and verification, do not duplicate it into separate planning, progress, and completion reports. The decision boundary matters more than the number of files representing it.

A general execution flow is:

```text
Read the request and current state
    -> Select the relevant domain context
    -> Establish scope and acceptance conditions
    -> Execute or delegate bounded work
    -> Check actual changes and verification evidence
    -> Update durable facts and remaining work
    -> Decide what follows
```

## Combine domain structure with time-based continuity

A solo workflow may emphasize “Where did I stop?” A team working in parallel may emphasize “Where is the contract for my area?” Both questions matter in both settings.

| Emphasis | Useful organizing structure | Risk to watch |
| --- | --- | --- |
| Continuity across sessions | Current state, active scope, decisions, and handoff | Detailed task history that is hard to connect to feature knowledge |
| Parallel domain work | Domain documents, shared contracts, and ownership | Several documents redefining the same shared contract |

These are design axes, not maturity levels. One developer with many independent features may need a strong domain map. A larger team with release dependencies may need explicit decision boundaries.

The useful combination is a simple route into a domain and enough task state to continue its current work. A scenario can explain how to use a contract, but should link to that contract rather than redefine it.

## Keep context current without erasing the reasons

Move durable implementation facts into their owning documents. Keep unfinished work in the active task record. Preserve important reasons and evidence where they can be traced, without making all history mandatory reading.

Git history can retain previous versions, but important current constraints should still be discoverable from current documents. “It exists somewhere in history” is not the same as “the next agent can find it.”

The goal is not fewer documents at any cost. It is fewer competing definitions and shorter paths to the information that matters.

## Try a small continuity test

A proposed check is to start a fresh session with the current documents and a task, but without the previous conversation. Ask it to identify the next permitted action, unresolved work, and unverified environments.

Record where reconstruction fails: a missing decision, an ambiguous scope, a stale link, or too much irrelevant context. Navigation time, unnecessary reading, and repeated explanations can be useful observations. They are not measured improvements until a comparison has actually been made.

Use the result to fix the smallest missing piece. Do not respond to every failure by adding another layer of process.

**The useful artifact is context that lets the next session make the right bounded decision—not an exhaustive archive it must read first.**

Related: [Cost-Aware Agent Orchestration](cost-aware-agent-orchestration.md)

# Capability Is Not Authority: Defining Permission Boundaries for Coding Agents

[All articles](../README.md) · [Model routing and observability](model-routing-and-observability.md)

A coding agent may have tools for editing files, running commands, changing a database, and publishing changes. Their availability does not make every action part of the current task.

At the same time, a clear implementation request should not become an endless sequence of questions about work that has already been authorized.

The design problem is to define a useful space for autonomous execution: enough authority to complete the task, with explicit boundaries around unrelated or externally consequential actions.

This article is a generalized design note. Examples are illustrative. It does not describe a particular product's permissions or demonstrate a measured reduction in incidents.

## 1. Ask two different questions

```text
Capability: Can the tool or agent perform this action?
Authority: Is this action permitted within the current task and constraints?
```

A connected publishing tool answers the first question. It does not answer whether a request to inspect a document includes publishing it.

Likewise, access to a database does not establish permission to change its schema. The ability to write a migration does not establish permission to apply it to an operational environment.

Authority needs to be understood in the context of the current request, the target environment, and continuing constraints. It is not a conclusion that follows from tool availability.

## 2. Treat permissions as separate dimensions

An implementation task can involve several kinds of action. Separate them when defining its scope:

| Dimension | Illustrative actions | Boundary to make clear |
| --- | --- | --- |
| Reading | Inspect source, documentation, logs, or database records | Which sources and data are relevant and permitted |
| Analysis | Diagnose a failure or propose a design | Analysis versus adopting a new product decision |
| Modification | Edit code, documents, or configuration | Which files and behaviors may change |
| Execution | Run builds, tests, commands, or migrations | Which commands, targets, and effects are included |
| Git operations | Stage, commit, push, or merge | Which operations and destination are authorized |
| Environment changes | Deploy or apply a database change | Which environment and change scope are included |
| Operational actions | Send notifications, publish content, or make paid requests | Which external effects are permitted |
| Product decisions | Change priorities, policies, or requirements | Which decisions remain with the owner |

This table is a way to describe scope, not a requirement to obtain eight approvals for every task. A single request can authorize several dimensions together.

For example, "implement the change, run the specified tests, and commit it to the named branch" can define a coherent work package. It need not imply permission to deploy, modify customer data, or change the product's requirements.

## 3. Make delegation boundaries concrete

A worker needs more than a goal. It needs to know what it may change and what it must return.

Here is a fictional assignment, not a copy of an operational prompt:

```text
Goal:
Update a notification-settings screen to match the agreed behavior.

Allowed:
Read the relevant interface and behavior documentation.
Modify the specified screen and its tests.
Run the relevant local checks.

Outside this assignment:
Unrelated refactoring.
Database changes.
Sending real notifications.
Committing, pushing, or deploying changes.
Changing product requirements.

Return:
The actual changes, checks performed, unresolved failures,
and any additional work that would require a different scope.
```

Another assignment may legitimately permit commits or database changes. The point is to state the actual boundary, not to reuse the same restrictive list everywhere.

Delegation should not silently expand the main agent's authority. A worker's ability to access a tool is not a substitute for understanding the permitted work.

Written boundaries describe intended behavior; they do not demonstrate that the tool environment enforces it or that a particular execution respected it. Those are separate verification questions.

## 4. Separate database preparation from database application

Database work makes the difference especially visible:

```text
Design a schema change
        ↓
Prepare a migration or SQL
        ↓
Identify the target environment
        ↓
Establish authority to apply the change there
        ↓
Apply and verify within that scope
```

The flow is a conceptual distinction, not a mandate for a new document or approval at each step.

A task may authorize preparing a migration without applying it. Another may include application to a disposable test database but not to production. The generated SQL alone cannot tell a later reader which of those actions happened.

Record the target and the actual result when an application is authorized. Keep preparation, application, verification, and recovery evidence distinguishable. This remains useful whether the executor is a person or an agent; the executor's identity does not replace the scope decision.

## 5. Do not turn a Git sequence into automatic permission

These are distinct operations:

```text
Edit → Stage → Commit → Push → Open a pull request → Merge → Release
```

The sequence is illustrative. Not every repository requires every step, and an article about permission boundaries should not impose a pull-request or review process on a personal notebook.

Permission to edit does not always include permission to push. Permission to push does not, by itself, authorize a deployment or a release. Conversely, when the user explicitly requests publication of specified material to a specified destination, repeatedly asking whether to perform that same publication adds no useful boundary.

Describe the authorized endpoint of the work. Is the expected result a local change, a commit, a remotely published document, or a deployed system? Avoid treating these as different names for "done."

## 6. Distinguish old task state from enduring constraints

An agent may encounter both of these statements:

```text
Earlier task note: analysis only
Current request: implement the agreed change
```

The relevant question is what the earlier statement represented.

If it described a previous stage of work, it should not automatically become a permanent prohibition. If it represented a continuing security, data-protection, or environment restriction, a new task verb is not enough reason to disregard it.

Both failure modes matter: treating stale progress notes as permanent constraints can prevent authorized work, while treating every new request as permission to discard continuing rules can expand scope incorrectly.

Resolve the distinction from the request and applicable constraints. This article does not propose a universal instruction hierarchy or a way to override the execution environment's restrictions. Where a material conflict remains unresolved, identify the specific affected action rather than inventing permission.

## 7. Finding a problem does not automatically authorize its repair

During an authorized change, an agent may discover an unrelated defect or a useful improvement.

Discovery supports a report: what was found, why it matters, and how it affects the current work. It does not automatically authorize every possible correction.

For a directly relevant issue, determine whether the necessary correction fits the existing scope. If it does not, describe the additional scope required. For an unrelated improvement, leave the current task focused and report the finding separately.

A potentially serious data-loss or security issue may justify pausing the affected action. That is different from using the discovery to make an unrequested operational change.

The intent is neither to ignore problems nor to absorb every nearby problem into the current assignment. It is to keep the relationship between a finding and its permitted response explicit.

## 8. Separate a completion report from acceptance

A worker can report what it did. Acceptance asks whether the result satisfies the original request within the permitted scope.

Useful evidence includes the actual changes, the checks that ran, their target, and unresolved failures. A summary of the worker's summary does not establish those facts by itself.

```text
Worker: execution and result report
        ↓
Main agent or owner: request, scope, changes, and checks compared
        ↓
Accept the confirmed result, request a correction, or preserve uncertainty
```

For a small task, one agent can perform both roles. The pattern does not require a second agent for every edit. The important question is what supports the completion claim, not how many participants appear in the workflow.

The verification effort should reflect the consequences of the change. A wording adjustment and an operational database change do not need identical procedures.

## 9. Use boundaries to enable autonomy

The purpose of minimum necessary authority is not to make the agent ask about every keystroke. It is to make the permitted work clear enough that it can proceed without repeated scope negotiation.

A practical assignment explains the goal, relevant inputs, allowed changes, target environment, excluded effects, and expected completion evidence. Once those are established, ordinary decisions inside that boundary can remain with the executor.

These distinctions do not prove that a system is safe, nor do they establish a measured improvement in speed or reliability. They provide a vocabulary for specifying intended behavior and examining whether actual actions stayed within it.

[Model Routing Is Not Model Observability](model-routing-and-observability.md) addresses a neighboring question: what was selected and what execution records actually show. [Cost-Aware Agent Orchestration](cost-aware-agent-orchestration.md) addresses when delegation is worth its overhead. Permission boundaries address something neither of those decisions can supply on its own: **what the chosen executor is allowed to do**.

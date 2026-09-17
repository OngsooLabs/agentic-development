# Model Routing Is Not Model Observability

[All articles](../README.md) · [Permission boundaries](agent-authority-boundaries.md)

A request to use a particular model is evidence of an intention. It is not, by itself, evidence of what executed.

This distinction matters when a main agent delegates work, a worker inherits a configuration, or an environment supplies defaults. A routing policy can look clear on paper while the execution report says little more than "inherited" or "unknown."

This article presents a general design pattern for keeping those claims separate. The examples and recording formats are illustrative, not a framework specification, an account of a particular runtime, or evidence of measured improvements.

## 1. Separate three questions

Model selection and execution observation answer different questions:

| Question | Information to record | What it does not establish on its own |
| --- | --- | --- |
| What was requested? | The requested model or profile, reasoning setting, and selection reason | That the environment applied those settings |
| What configuration was resolved? | The configuration selected through an explicit request, inheritance, or defaults, when available | That execution evidence confirms the resolved settings |
| What was observed? | Model and reasoning information actually exposed by execution records, with its source | Any setting those records do not expose |

A useful conceptual sequence is:

```text
Requested configuration
        ↓
Resolved configuration, if available
        ↓
Observed execution information
```

The values may agree. The point is not that they must differ, but that each claim needs its own basis.

A request record can establish what was sent. A configuration record can establish what the environment selected. An execution record may establish additional details. Describe what each record actually shows rather than treating all three as interchangeable.

## 2. Distinguish explicit selection, inheritance, defaults, and unknowns

The following terms are useful working vocabulary, not standardized status names:

| Term | Meaning in this pattern |
| --- | --- |
| **Explicit** | A model or reasoning setting was deliberately specified for the task. |
| **Inherited** | The task was deliberately configured to use a parent or shared setting. |
| **Defaulted** | No task-specific choice was made and the environment's default was used. |
| **Unknown** | Available evidence does not establish a requested execution detail. |

These terms should not hide the difference between selection and observation. An explicit request can still have an unknown observed model. Intentional inheritance can have a known observed model. Record both rather than forcing them into a single success label.

Consider this illustrative record:

```text
Selection mode: explicit
Requested model: coding-profile
Requested reasoning: high
Selection reason: a change spanning several modules
Observed model: unknown
Observed reasoning: unknown
Observation source: the available report does not expose either field
```

Here, `coding-profile` is a placeholder, not an actual model identifier or executable setting.

The correct conclusion is that the requested configuration is known and the execution configuration remains unconfirmed. Neither "the requested model definitely ran" nor "the runtime definitely ignored the request" follows from this record.

Likewise, "inherited" is not automatically a defect. The question is whether inheritance was intentional, what it was expected to inherit, and what can actually be observed.

## 3. Classify the work before selecting the model

Routing becomes easier to explain when the decision starts with the work rather than a favorite model name.

For a mechanical check, first ask whether a deterministic tool is sufficient. For broad discovery, consider the ability to find relevant evidence across files. For implementation, consider the change scope and necessary tests. For a high-risk change, consider the reasoning and verification needed to examine its consequences.

A routing explanation should connect the selected role or configuration to those requirements. "Use an appropriate model" is an intention, not a selection procedure.

Two mechanisms can coexist:

**A central policy** supplies the normal choice for a class of work. This keeps the shared decision in one place.

**A task-specific override** records a deliberate exception and its reason. This makes exceptional requirements visible without copying the entire policy into every task.

An omitted override does not, by itself, explain whether the task intentionally inherited a setting or simply fell back to a default. Preserve that distinction when the environment allows it.

This is about making a choice understandable, not prescribing a particular model hierarchy. The broader delegation trade-offs are covered in [Cost-Aware Agent Orchestration](cost-aware-agent-orchestration.md).

## 4. Keep a small configuration-and-evidence record

A minimal record can be plain text:

```text
Task or role:
Selection mode: explicit / inherited / defaulted / not established
Selection reason:
Requested model or profile:
Requested reasoning:
Resolved configuration, if exposed:
Observed model:
Observed reasoning:
Observation source:
Unconfirmed details:
```

Use only fields that are meaningful in the environment. A system that does not expose a reasoning setting should not acquire an invented one just to complete the form.

The purpose of the observation source is to distinguish a value copied from the request from a value obtained from execution evidence. If a report merely repeats the requested settings, label it accordingly.

Preserve partial knowledge. When a record identifies a model but does not identify the reasoning setting, report the model and leave reasoning unconfirmed. Do not turn one known field into confirmation of every field.

The same principle applies to inheritance: a known shared policy is useful context, but it should not be silently substituted for a missing execution observation.

## 5. Test the behavior, not only the policy text

Editing a routing instruction is not the same as showing that routing behavior changed.

One proposed diagnostic exercise is to use several bounded tasks: a small lookup, a moderate implementation in a test workspace, and a simulated higher-risk change. Before running them, state the expected selection behavior under the policy being tested.

Then compare three things:

```text
Expected routing under the policy
        ↓
Actual request or resolved configuration
        ↓
Available execution observations
```

The exercise is not a contest to make every task use a different model. A policy may legitimately select the same configuration for several tasks. The question is whether the selection and its explanation match the intended policy.

A request record may show that a new override was sent, while execution metadata remains unavailable. That supports a narrow conclusion: the request behavior changed. It does not establish every detail of runtime execution.

Keep these claims separate:

```text
Policy text changed
    ≠
Request or routing behavior changed
    ≠
Execution settings were confirmed
    ≠
Cost or quality improved
```

Testing this pattern does not require applying risky changes to a live environment. Illustrative or isolated tasks can exercise the decision boundary without granting unrelated operational authority.

## 6. Understand the trade-offs

Explicit settings make intent easier to inspect, but repeating them everywhere can create many places to update. Central policies make shared changes simpler, but unexplained inheritance can hide why a task received a particular setting.

Observation records help separate these questions; they do not make one configuration inherently better than another.

Without execution information, attributing a cost or quality change to a model choice remains uncertain. Conversely, knowing the execution settings is not enough to prove that they caused an improvement. Work scope, input context, retries, and verification effort still matter in a comparison.

Keep durable guidance focused on roles, risk, required verification, and cost or latency constraints. Preserve time-specific settings in the appropriate experiment record rather than treating a model identifier as a permanent methodology.

## 7. What this pattern establishes—and what it does not

The practical goal is a more precise account of a delegated task:

> This is what we requested, this is how the choice was made, this is what the available records show, and this is what remains unknown.

That is useful even when the environment exposes little metadata. An explicit unknown is more informative than unsupported certainty.

This article does not establish that explicit routing outperforms inheritance, that a higher reasoning setting improves every task, or that adding these records reduces costs. Those are separate questions requiring their own evidence.

Model routing asks **who should do the work**. Observability asks **what the execution evidence shows**. Neither determines **what that executor may do**; that is the subject of [Capability Is Not Authority](agent-authority-boundaries.md).

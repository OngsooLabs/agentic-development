# Evidence-Carrying Content and the Limits of Automated Playtesting

[Article index](../README.md)

**A generator can provide a path to a goal. A playtest can show that a particular path works. Neither observation, by itself, shows that a new player will discover the path or enjoy doing so.**

This article connects two design questions: what evidence should accompany procedural content, and what can automated playtesting establish about it? The pipeline and examples are illustrative. They are not a report of a released game's implementation, measured productivity, or human playtest results.

## Generate a witness, not only an artifact

Consider a fictional tile puzzle. Rather than exporting only a board and a target score, a generator can also export a legal action sequence that reaches the target under a specified ruleset. That sequence is a solution witness: a concrete path whose steps and outcome can be checked.

A proposed pipeline separates responsibilities:

```text
Check reference cases against the rules
    -> Generate candidate data and an action witness
    -> Export, reload, and replay the saved candidate
    -> Estimate difficulty using a separate evaluation procedure
    -> Select a sequence of candidates
    -> Add presentation and check game integration
```

The witness must satisfy the stated objective, not merely produce a non-error result. For example, exhausting a deck is different from reaching the target score and satisfying all required placements. Record the initial state, legal actions, terminal condition, and outcome needed to check that distinction.

Replaying the reloaded artifact matters because in-memory generation does not exercise serialization, asset references, or import behavior. Use the game's actual rules where practical instead of maintaining a second, subtly different scoring implementation. This checks consistency with those rules; it does not independently prove that the shared rule implementation matches the intended design. Reference cases and specification-focused checks still have their own role.

A useful evidence package connects the saved content to its generation request, seed, relevant rule and generator versions, action trace, target, and replay result. Keep failed or incomplete candidates distinguishable from accepted ones.

## Reproducibility needs more than a seed

A seed identifies one input to generation, not the whole procedure. Candidate ordering, allowed content, rules, search limits, evaluator behavior, and selection settings can also affect the result.

Save the request for the operation being reproduced. An individual candidate request may not reconstruct why a sequence selector rejected earlier attempts and chose that candidate. Version the relevant procedure and preserve the selected artifact rather than assuming it can always be regenerated from a short label.

Presentation changes should not silently consume gameplay randomness or change logical state. When an appearance is chosen during authoring, preserve that choice rather than rerolling it on load. The reproducibility claim remains scoped to the relevant code, data, and assets; it is not a promise that future versions render identically.

A search budget should produce an honest terminal result. Reaching the budget means the requested search was not completed or did not find a candidate within that budget. It does not establish that a solution is impossible. If too few candidates meet the selection constraints, report the shortfall instead of quietly weakening the constraints.

## Keep solvability, difficulty, and enjoyment separate

A successfully checked witness supports an existential claim: this target is reachable along this path under these rules and conditions. It does not establish optimality, a unique solution, ease of discovery, or enjoyment.

A difficulty evaluator can run bounded strategies using the information available to a player. Its success rate or recovery behavior describes those strategies under those limits. Failure to find a solution is not evidence of impossibility when a valid witness already exists.

Do not let an evaluator consult hidden future information and then present its results as player-visible decision difficulty. Also distinguish normal success from success after a forced alternative action: they answer different questions about the tested strategy.

Difficulty labels can initially be authoring heuristics, but should remain labeled as estimates until compared with relevant player observations. Selecting candidates that fit an automated label does not validate that label against human experience. A generator and an evaluator can share blind spots even when they have different class names or run in different processes.

## Automated playtesting has an evidence boundary

The input path matters as much as the final screen.

| Test route | Evidence it can supply within the tested scope | What remains outside that evidence |
| --- | --- | --- |
| Rules-only action replay | Legal transitions and achievement of the stated objective | Finding and operating the visible controls |
| Direct UI-handler invocation | Behavior downstream of the invoked handler | Hit testing and ordinary pointer delivery |
| Editor input-event injection | Behavior through the exercised editor input path | Physical-device interaction and performance |
| Automated screen input on an installed build | The exercised input and display flow on that build and environment | Human discoverability, comfort, and enjoyment |
| A solution-informed agent | Successful execution of a known path | First-time reasoning needed to discover it |

These routes are not a universal maturity ladder. A rules test can isolate a scoring error more clearly than a screen test; a screen test can reveal a hit-target problem that direct handler invocation bypasses. Choose the route from the claim being checked and preserve the distinction in the result.

The same applies to persistence. Restoring an object from serialized data, restarting an application process, and reinstalling an application are different tests. Record the one performed rather than summarizing all of them as save recovery.

A fixture that unlocks later content enables a targeted test. It is not evidence that the observer played every preceding stage. Repeating the same stage is useful regression work, but does not create additional independent first-time players.

## Record what the observer already knew

An agent that has read source code, scoring rules, or a reference solution has different information from a newcomer. Even an observer instructed not to read documentation may see explanatory text on screen or retain knowledge from a previous run.

Describe blind testing through explicit knowledge restrictions and observed exposure, not through the label alone. Record whether the observer knew the solution, had played previously, saw rule text, or used a prepared progression state. A restricted-context agent observation can identify candidate usability problems, but does not become human evidence merely because it was called blind.

Keep observations and interpretations separate. For example, an agent may report that two highlights looked similar while still selecting the intended cell. That supports a visual observation and a hypothesis about confusion, not a measured human error rate.

## Tie conclusions to the tested artifact

A compact record can include the build or source identity, relevant uncommitted inputs, installed artifact identity when available, stage and request, input route, observer knowledge, exercised behavior, actual outcome, and unverified scope. A filename such as latest is not sufficient evidence that the installed binary matches current source.

Do not inflate a completed trace into a broad quality claim. State which mechanics, presentation, and integration paths were checked. Keep human questions such as discoverability, frustration, perceived difficulty, and desire to replay as separate observations to collect rather than assigning them the technical test's result.

**The useful output is not simply generated content or an agent's clear screen. It is content and observations whose evidence is strong enough for the specific decision being made.**

Related: [Benchmark Design](benchmarking-quality-performance-and-reliability.md) · [Bounded Product Changes](from-benchmark-findings-to-product-changes.md) · [Cost-Aware Orchestration](cost-aware-agent-orchestration.md)

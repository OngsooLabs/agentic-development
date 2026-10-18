# When an Experiment Fails: Stop Conditions for AI-Driven Development

[Article index](../README.md)

**An iteration is not progress unless it moves an experiment toward a predefined decision.**

AI-assisted development makes it easy to generate another heuristic, adjust a threshold, or ask for one more implementation. That speed can turn an experiment into an endless sequence of changes whose success criteria drift whenever a result disappoints.

## Define the decision before tuning

Before implementation, define the primary outcome, baseline, sample or evaluation set, minimum result that justifies continuing, maximum tuning effort, and stop conditions for success, failure, invalid data, or missing prerequisites.

A stop condition is a decision rule, not just a deadline. For example, after a predefined number of adjustments fails to meet the primary criterion, preserve the results and stop until a materially new hypothesis or better evidence is available. The number of attempts is illustrative and should be fixed before the experiment.

## Keep secondary improvements secondary

A change may improve runtime or an intermediate metric while failing to improve the primary objective. Record the partial effect, but do not silently replace the primary criterion with whichever metric improved.

Separate four conclusions:

1. **Implementation:** the change behaved as intended in the tested environment.
2. **Metric:** the measured outcome improved, worsened, or remained inconclusive.
3. **Product:** evidence supports the intended user experience or business objective.
4. **Decision:** continue, revise the hypothesis, gather different evidence, or stop.

A strategy can execute correctly without outperforming a simple baseline. A small sample can fail to establish an advantage without proving that no advantage is possible.

## Preserve negative and inconclusive results

Keep comparison conditions, failed runs, exclusions, and reasons for stopping. Do not remove inconvenient samples or change the scoring rule after seeing the result unless the new analysis is labelled exploratory and a fresh confirmation set is used.

If prerequisites are missing, report that the experiment did not run. Zero observations are not a negative user result. Preparing a human study is not the same as collecting human observations.

## Reopen only when evidence changes

A stopped experiment can be reopened for a materially different hypothesis, a corrected measurement defect, a new evaluation set, or another concrete reason that changes the expected value of another attempt. Repeating a similar adjustment without new evidence is not independent confirmation.

A useful report names the tested version, baseline, primary criterion, sample size, adjustments attempted, observed outcome, limitations, and what was not tested. If human evaluation did not occur, state that explicitly.

**The goal is not to make every experiment succeed. It is to make each experiment produce a trustworthy decision, including the decision to stop.**

Related: [Cost-Aware Agent Orchestration](cost-aware-agent-orchestration.md) · [Benchmarking Quality, Performance, and Reliability](benchmarking-quality-performance-and-reliability.md)

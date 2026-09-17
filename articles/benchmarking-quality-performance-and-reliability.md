# Benchmarking Quality, Performance, and Reliability

[Article index](../README.md)

**A benchmark should make different kinds of failure visible before compressing them into a score.**

For an API that retrieves or transforms external content, a successful request, a usable body, an accurate result, and a timely result are different observations. A single success rate can hide which one was measured.

This article generalizes measurement questions into a benchmark design pattern. It does not report provider rankings, product results, or a completed experiment.

## Define success at each layer

Use separate fields for transport status, acquisition outcome, provider-reported status, and independently assessed quality.

```text
Successful HTTP response
    does not establish
Successful acquisition of the intended content
    does not establish
A complete and useful result under the evaluation criteria
```

An illustrative content transformation could return a long body containing mostly navigation, or a short body missing its main section. Length and a success flag would not resolve either quality question.

A working measurement map might look like this:

| Dimension | Question |
| --- | --- |
| Acquisition | Was a usable result obtained? |
| Accuracy | Does the result match the source? |
| Completeness | Are important sections or structures missing? |
| Cleaning | How much irrelevant or duplicate material remains? |
| Structure preservation | Are meaningful headings, lists, and links retained? |
| Latency | How long did an attempt or the complete request take? |
| Repeatability | How much do outcomes vary across runs? |
| Operational reliability | What happens under limits, timeouts, and dependency failures? |

This is a practical decomposition, not an official taxonomy. Adapt it to the result the user actually needs.

## Do not let a retry erase the first attempt

Consider this fictional sequence:

```text
Attempt 1: rate-limited
Attempt 2: rate-limited
Attempt 3: content received
Final quality assessment: incomplete
```

Several statements are true: acquisition eventually succeeded, the first attempt failed, waiting and retries contributed to elapsed time, and the final body did not meet the quality criterion.

Reducing that sequence to “success” loses most of its meaning.

Record the initial outcome, every permitted retry, the terminal outcome, and quality separately. Distinguish the number of attempted requests from the number of individual attempts. Preserve scheduled cases that were not executed as their own category.

## Treat rate limits as operational evidence

A 429 response does not describe the accuracy of content that was never obtained. It still matters to the ability to obtain a result.

It can therefore be appropriate to evaluate body quality only where a body exists, while reporting acquisition failures and their counts alongside that conditional quality result. The quality subset must not replace the full request population.

For rate-limited cases, preserve whether the limit occurred initially, whether retries recovered, which retry policy applied, and whether the request ultimately failed. Include relevant host or dependency distinctions when interpreting the result.

Excluding a case from a particular metric is not permission to delete its history.

## Report latency with its population

Successful-request latency answers how quickly successful requests completed. It does not answer how often a user received a successful result.

Report it alongside initial success, eventual success, timeout and other failure counts, and end-to-end time under the chosen retry policy. Keep time spent waiting for retries distinguishable from one attempt's processing time where the instrumentation supports that distinction.

A fictional system that succeeds quickly but fails often and one that succeeds slowly but consistently should not become indistinguishable through an unexplained average.

State which observations enter each latency summary. Failure records remain relevant even when they do not enter a success-only distribution.

## Fix the protocol before seeing the results

Write down the input set, rubric, and execution conditions before comparing outcomes. In particular, define maximum attempts, retryable errors, timeout, backoff, handling of `Retry-After`, concurrency, request spacing, host limits, and exclusion rules.

Preserve the dataset identity or hash, rubric version, relevant implementation/API versions, run environment, and cache conditions. A dataset hash identifies the saved input list; it does not establish that a changing external source remained unchanged.

Keep attempt records sufficient to explain the final aggregates. Fixing a test tool or changing the protocol should not silently replace the meaning of an earlier run. Preserve the distinction between comparable reruns and runs under changed conditions.

Passing a mock or fixture test also does not establish that a live dependency behaved the same way. Record which environment the evidence describes.

## Separate measurement judgments from product decisions

Measurement is not judgment-free. Someone must determine whether data is valid, whether the protocol was followed, and how a case fits the predefined rubric.

The useful separation is between these judgments and conclusions about product direction:

```text
Measurement A -> observations, predefined classification, validity
Measurement B -> observations, predefined classification, validity
                                |
                                v
Decision -> suitability for a use case and improvement priorities
```

Do not change classification or exclusion rules to support a preferred product conclusion. Defer rankings, positioning, and investment choices until the relevant measurements are available.

If an aggregate score is needed, make its weights and rationale explicit. Prefer choosing them before seeing the outcomes; a composite should not conceal its component results.

## Different capabilities need different tests

A one-shot content transformation and a recurring change detector may share dependencies but promise different outcomes.

The former needs acquisition, completeness, cleaning, and response-time measurements. The latter needs missed-change and false-alert observations, detection delay, and repeated-operation reliability. Evidence about one cannot simply stand in for evidence about the other.

When analysis uses an AI judge, distinguish that external evaluation tool from any quality checks already inside the tested service. A common evaluation procedure should be applied consistently across outputs rather than treating one provider's internal self-assessment as the shared reference.

## Keep results tied to the question

Before interpreting a result, ask whether inputs and conditions are comparable, failures and retries remain visible, each metric's population is clear, and the evaluation criteria answer the intended use case.

A result can identify an acquisition bottleneck without settling extraction quality. It can identify missing content without explaining latency. That separation is useful because the corresponding improvements are different tasks.

**The aim is not to produce the cleanest success column. It is to preserve enough structure to explain what succeeded, what failed, and which decision the evidence can support.**

Related: [AI Judge Design](designing-ai-judges-for-quality-validation.md) · [Cost-Aware Orchestration](cost-aware-agent-orchestration.md)

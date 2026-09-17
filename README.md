# Agentic Development

Practical learning notes on **context design, agent orchestration, quality evaluation, and benchmarking** in AI-assisted software development.

The central question is not simply which model to use. It is how to help an agent find the right context, work within a clear scope, and connect its conclusions to evidence.

These articles turn development observations and design discussions into general patterns. They are not a framework, a formal maturity model, or documentation of any product's current implementation.

## Articles

| Question | Article |
| --- | --- |
| How can one developer preserve decisions across sessions and agents? | [Context Architecture for Solo Agent-Driven Development](articles/context-architecture-for-solo-agent-development.md) |
| When is delegation worth its coordination and verification cost? | [Cost-Aware Agent Orchestration](articles/cost-aware-agent-orchestration.md) |
| How should quality, latency, and failed requests be measured separately? | [Benchmarking Quality, Performance, and Reliability](articles/benchmarking-quality-performance-and-reliability.md) |
| How can semantic evaluation complement rules without becoming unquestioned truth? | [Designing AI Judges for Quality Validation](articles/designing-ai-judges-for-quality-validation.md) |

Start with context architecture for project continuity, orchestration for task execution, or either evaluation article for measurement design. There is no required reading sequence.

## How to read these notes

The patterns are starting points for reasoning about trade-offs, not universal prescriptions. Solo and team workflows can combine domain-based documentation with time-based task continuity. Delegating more tasks does not by itself establish lower cost or better results. A successful API response does not by itself establish useful output.

Examples are illustrative unless explicitly described otherwise. These articles do not publish experimental scores, cost savings, model rankings, or claims of production readiness. Suggestions for measurements are not completed measurements.

The articles are generalized rewrites, not complete experimental reports or copies of operational documents. Internal identifiers, raw records, deployment details, and product-specific routing policies are outside their scope.

## Keeping the collection useful

Use this README as the single article index. Improve an existing article when a new observation fits its question; add an article only for a genuinely different question. Keep facts, interpretations, and untested ideas distinguishable, and preserve meaningful changes through Git history.

There is no required document review, approval state, or publication ladder. Editing guidance for agents is in [AGENTS.md](AGENTS.md).

The aim is to share what is worth studying, trying, and questioning—not to turn every development task into a larger process.

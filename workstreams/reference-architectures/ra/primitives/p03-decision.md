# P3 Decision

**Obligation:** MUST
**Introduced by:** [Single-agent workflow RA](../ra-single-agent.md)

## Definition

A branch resolved by an explicit rule.

## Why it exists

Without an explicit, reviewable branch construct, routing logic gets buried inside prompts or ad hoc code, and the confidence-based routing this RA depends on (`AgentStep` output to `Decision` to `HumanCheckpoint` or auto-complete) has nowhere to live.

`P3` is the deterministic complement to `P4 AgentStep`: every place this RA says "route on confidence" or "route among a known set of handlers" resolves to a `P3`. It is also the target of the determinism-hints guidance for "routing among a known set of handlers": an enumerable handler set is exactly what a `Decision` resolves, and agent judgment should be reserved for the residual case that fits no known handler.

## Prior art

| System | Nearest equivalent |
| --- | --- |
| BPMN 2.0 | Gateway + business rule task (DMN) |
| n8n | IF / Switch node |
| AWS Step Functions | Choice state |
| Temporal | Ordinary code branch |
| LangGraph | Conditional edge |

Nearest equivalent is editorial judgment, not a conformance claim.

## Relationship to other primitives

- [P4 AgentStep](p04-agent-step.md): the output contract's confidence signal is what makes a confidence-based `Decision` possible after an agent step.
- [P6 HumanCheckpoint](p06-human-checkpoint.md): the typical low-confidence branch target.
- [P14 OutcomeContract](p14-outcome-contract.md): decisions downstream of an `AgentStep` are deterministic all the way to the outcome classification.

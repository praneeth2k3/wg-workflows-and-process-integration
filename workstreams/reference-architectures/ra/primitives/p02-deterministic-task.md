# P2 DeterministicTask

**Obligation:** MUST
**Introduced by:** [Single-agent workflow RA](../ra-single-agent.md)

## Definition

A fixed-function step: given the same input, it always takes the same path.

## Why it exists

Without a named "this is not the agent" step, every step in a workflow looks equally suspect for agentic reasoning, and the checklist's most common correct answer, "pure deterministic workflow, no `AgentStep` needed," has no explicit target to route to.

`P2` is the majority case, not the exception, and it needs its own name specifically so it is not quietly reimplemented as an `AgentStep` for convenience, or because a stakeholder wanted "AI" somewhere in the process. The determinism-hints guidance is explicit about where this primitive belongs: closed-set classification with stable labels, arithmetic, eligibility, thresholds, entitlement, and routing among a known set of handlers are all `P2` work, never delegated to a model. Retry and idempotency logic also belong here (to the orchestrator, not the agent) because agents retry non-idempotently and without the orchestrator's visibility.

## Prior art

| System | Nearest equivalent |
| --- | --- |
| BPMN 2.0 | Service task / script task |
| n8n | Action node |
| AWS Step Functions | Task state |
| Temporal | Activity (deterministic code path) |
| LangGraph | Node |

Nearest equivalent is editorial judgment, not a conformance claim.

## Relationship to other primitives

- [P3 Decision](p03-decision.md): the deterministic branch a `P2` step often feeds into or follows.
- [P4 AgentStep](p04-agent-step.md): the checklist's first question ("can the task be fully enumerated at design time?") routes here when the answer is yes.
- [P9 ErrorBoundary](p09-error-boundary.md): retry and idempotency logic belongs to the orchestrator's deterministic layer, not to an agent.

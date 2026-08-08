# P5 ToolCall

**Obligation:** MUST
**Introduced by:** [Single-agent workflow RA](../ra-single-agent.md)

## Definition

An agent-initiated effect on an external system: the point at which an `AgentStep` reaches outside itself.

## Why it exists

Without a distinct primitive for "the agent touched something outside itself," effect classification — `read_only`, `reversible_write`, `irreversible` — has nothing to attach to, and the irreversibility gate cannot be enforced because there is no declared point where an effect occurs.

`P5` is the unit the tool-scope allowlist governs, and it must be a workflow-level primitive precisely because the Model Context Protocol itself disclaims protocol-level enforcement of tool trust. The [MCP specification (2025-06-18)](https://modelcontextprotocol.io/specification/2025-06-18) states plainly that tool descriptions and annotations from untrusted servers should be considered untrusted, and that MCP "itself cannot enforce these security principles at the protocol level." The implication is direct: the workflow, not the tool server, is the authority on a tool call's effect classification and on what consent that classification requires. An MCP server's own tool annotation is, at best, a hint; the classification MUST be asserted by the workflow's tool registry, not inferred at call time from server-supplied hints alone.

> **[verify]** The exact MCP tool annotation field names (reported elsewhere as `readOnlyHint`, `destructiveHint`, `idempotentHint`, `openWorldHint`) were not confirmed against the specification text for this draft. Confirm against the MCP schema before publication, and before asserting any binding between them and this RA's effect classes.

## Prior art

| System | Nearest equivalent |
| --- | --- |
| BPMN 2.0 | Activity inside the ad-hoc sub-process |
| n8n | Sub-node attached to the AI Agent via a typed tool connection |
| AWS Step Functions | No dedicated state — nested Task invoked by the agent runtime |
| Temporal | Activity invoked from within the `AgentStep`'s Activity/child workflow |
| LangGraph | Tool node |

Nearest equivalent is editorial judgment, not a conformance claim.

## Relationship to other primitives

- [P4 AgentStep](p04-agent-step.md) — the tool-scope allowlist (contract element 4) governs which `P5 ToolCall`s an agent may make.
- [P6 HumanCheckpoint](p06-human-checkpoint.md) — required in front of any `irreversible` tool call, unless a deterministic policy gate substitutes for it.
- [P9 ErrorBoundary](p09-error-boundary.md) — a failed tool call MUST NOT be retried by the agent itself; retry is orchestrator-owned.
- [P11 Compensation](p11-compensation.md) — the undo path when a `reversible_write` tool call turns out to be wrong.
- Security & Privacy WG — this RA emits the effect classification and identity/delegation scope as the surface their threat modelling operates on; it does not perform that analysis itself.

# Open Research Questions

- What would a portable, cross-runtime representation of `P13 Budget` and `P18 ArbitrationPolicy` actually look like in a concrete schema, and should it be proposed as an extension to an existing standard (BPMN `extensionElements`, an MCP extension, an A2A extension) or as new, WG-owned notation?

- Does `P14`'s seven-value classification vocabulary belong in `P14`'s core definition in `ra-single-agent.md`, or is it properly a multi-agent-specific elaboration that belongs only in `ra-multi-agent.md`'s interop layer? [`reconciliation-log.md`](reconciliation-log.md) flags this as an asymmetry between the two documents that the WG should resolve explicitly rather than leave implicit.

- Should the Workflow Taxonomy deliverable (targeted the same date as the Workflow Reference Architecture) be drafted as a direct extension of the primitive set, or does the WG intend two independently-developed vocabularies to be reconciled after the fact?

- Is the T1 supervisor best modelled as a constrained instance of `P4 AgentStep`, or does it deserve to be a distinct primitive, given how central the "supervisor MAY be deterministic" variant already is to `ra-multi-agent.md`'s default recommendation? (`ra-multi-agent.md`'s own open questions ask this already; it is repeated here because the primitive-by-primitive analysis — now in [`ra/primitives/`](../ra/primitives/README.md) — did not need a P19 to answer it at the time this question was first raised, which was itself mild evidence the primitive set then in place may already have been sufficient.)

- How should this document's own industry mapping be kept current as vendors (Camunda, n8n, LangGraph, and others) ship new agentic features on a much faster release cadence than this WG's deliverable cycle? A landscape survey with no update mechanism goes stale in a domain moving this fast.

- Should `P11 Compensation`'s obligation level move from SHOULD to MUST for any `AgentStep` with a `reversible_write` tool in scope, given the regression argued in [`P11 Compensation`'s own file](../ra/primitives/README.md)? This document takes a position; it is not this document's place to change the RA files' normative table.

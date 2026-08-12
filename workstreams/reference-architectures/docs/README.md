# Docs

Supporting material for the reference architectures workstream.

Use this folder for background notes, comparisons, source summaries, exploratory drafts, and other supporting material that informs the reference architectures without being one of them.

This folder is non-normative. Nothing here uses RFC 2119 / BCP 14 keywords (MUST, MUST NOT, SHOULD, SHOULD NOT, MAY) as an assertion of its own. The two RA files (`ra-single-agent.md`, `ra-multi-agent.md`) carry that vocabulary because they specify a contract; this folder is analysis and evidence-gathering. Where a keyword appears below, it is inside a direct quotation from a cited source or from the RA files themselves, not a claim this folder is making on its own authority.

Primitive-by-primitive justification (the argument for why each primitive looks the way it does, its prior art across the surveyed systems, and its own `[verify]` markers where one applies) now lives with the primitive itself, one file per primitive, under [`../ra/primitives/`](../ra/primitives/README.md). This folder does not carry a standalone primitive-justification document; it did once, and retiring it in favor of primitive-local justification is recorded in [`reconciliation-log.md`](reconciliation-log.md).

## Index

### Landscape survey

The industry and standards landscape this workstream surveyed, one file per system family, indexed at [`landscape/README.md`](landscape/README.md):

- [`landscape/process-standards.md`](landscape/process-standards.md): BPMN 2.0, DMN, CMMN
- [`landscape/durable-execution.md`](landscape/durable-execution.md): Temporal, Dapr Workflow / Dapr Agents, Microsoft Durable Functions, Restate, DBOS
- [`landscape/low-code-orchestration.md`](landscape/low-code-orchestration.md): n8n, Zapier, Make, Workato
- [`landscape/cloud-state-machines.md`](landscape/cloud-state-machines.md): AWS Step Functions, Azure Logic Apps, Google Cloud Workflows
- [`landscape/agent-frameworks.md`](landscape/agent-frameworks.md): LangGraph, CrewAI, Microsoft AutoGen / Agent Framework, OpenAI Agents SDK
- [`landscape/vendor-neutral-specs.md`](landscape/vendor-neutral-specs.md): CNCF Serverless Workflow
- [`landscape/interop-protocols.md`](landscape/interop-protocols.md): MCP, A2A

### Supporting analysis

- [`why-a-reference-architecture.md`](why-a-reference-architecture.md): why a reference architecture is needed now: the vocabulary gap, the architectural (not model-quality) nature of the failure mode, and evidence that agentic execution is already being retrofitted onto pre-agentic standards.
- [`gap-analysis.md`](gap-analysis.md): the gaps the landscape survey surfaces, and which cluster is tractable within this WG's own drafting cycle versus which requires sustained cross-organisational coordination.
- [`counter-arguments.md`](counter-arguments.md): five objections to this RA's approach, with responses.
- [`charter-mapping.md`](charter-mapping.md): how the RA content maps to the WG charter's scope items, and the case for aligning the Workflow Reference Architecture and Workflow Taxonomy deliverables rather than developing them independently.
- [`open-research-questions.md`](open-research-questions.md): open questions for the WG, not yet answered by either RA file.
- [`reconciliation-log.md`](reconciliation-log.md): a running log of discrepancies found across the RA files and this `docs/` folder, and how each was dispositioned.
- [`references.md`](references.md): the reference list, grouped by source type, with a source-quality note.

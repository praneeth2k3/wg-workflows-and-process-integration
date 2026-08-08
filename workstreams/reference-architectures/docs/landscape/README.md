# The Comparison Landscape

This is the industry and standards survey that informs the primitive vocabulary and topology catalog defined in [`../../ra/`](../../ra/). It is descriptive, not normative: no file in this folder uses RFC 2119 / BCP 14 keywords as an assertion of its own, except inside direct quotations from a cited source.

Facts marked `> **[verify]**` need source confirmation before external publication. Everything else is drawn from the reference list in [`../references.md`](../references.md), from the two RA files, from the charter, or from the prior proposal — or is presented explicitly as this survey's own argument.

## System families

- [`process-standards.md`](process-standards.md) — **BPMN 2.0, DMN, CMMN.** Mature OMG standards for deterministic process flow, decision logic, and knowledge-work case management, respectively; the only family here with an extension mechanism (`extensionElements`) already used to host agentic configuration in production.
- [`durable-execution.md`](durable-execution.md) — **Temporal, Dapr Workflow / Dapr Agents, Microsoft Durable Functions, Restate, DBOS.** Code-first orchestration that survives process restarts by replaying or resuming from a durable log; none was designed with an LLM call in mind, which is exactly why the determinism-vs-non-determinism tension these systems solve for ordinary side effects generalizes cleanly to agentic reasoning.
- [`low-code-orchestration.md`](low-code-orchestration.md) — **n8n, Zapier, Make, Workato.** Visual, connector-driven automation for non-developers, now layering agentic capability onto a fixed automation model; the population actually meeting agentic workflow concepts for the first time, through the UI rather than the notation.
- [`cloud-state-machines.md`](cloud-state-machines.md) — **AWS Step Functions, Azure Logic Apps, Google Cloud Workflows.** Declarative, cloud-native state machines shipped independently by three vendors, closer in spirit to code than to graphical process notation.
- [`agent-frameworks.md`](agent-frameworks.md) — **LangGraph, CrewAI, Microsoft AutoGen / Agent Framework, OpenAI Agents SDK.** Where multi-agent coordination constructs — handoff, group chat, hierarchical delegation — already exist as product features, each with its own vocabulary and no shared interchange format between them.
- [`vendor-neutral-specs.md`](vendor-neutral-specs.md) — **CNCF Serverless Workflow.** The closest existing precedent for a vendor-neutral workflow DSL governed under a foundation family adjacent to AAIF's own — both an ally and a cautionary tale, kept separate because it is expected to grow as a point of comparison.
- [`interop-protocols.md`](interop-protocols.md) — **MCP, A2A.** Protocols for, respectively, the tool-integration surface between a model-hosting application and its tools, and peer-to-peer agent delegation across a boundary where no shared orchestrator exists.

## Source-quality note

See [`../references.md`](../references.md) for the full reference list and its source-quality note. In short: the OMG specifications, the MCP and A2A specifications, the Linux Foundation press releases, NIST, and OWASP are primary or standards sources; the vendor documentation entries (Camunda, Temporal, Dapr, LangGraph, CrewAI, Microsoft AutoGen, OpenAI Agents SDK, n8n, AWS Step Functions, Azure Logic Apps, Google Cloud Workflows) reflect how each vendor describes its own product, not independent verification.

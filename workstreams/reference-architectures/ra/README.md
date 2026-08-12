# Reference Architectures: `ra/`

This directory is the normative reference-architecture set for the Workflows & Process Integration WG's reference-architectures workstream. Everything under `ra/` is the RA: the two top-level documents, the primitive definitions, the two contracts, the six topologies, and the shared guidance. [`../docs/`](../docs/) is the opposite kind of thing: supporting material that informs this RA set but does not define it, meeting notes, comparison tables, source summaries, exploratory drafts. See the [workstream README](../README.md)'s "Side docs" section for that split in full. If a claim in `ra/` needs a citation, it should point to something in [`guidance/references.md`](guidance/references.md) or to `docs/`, not the other way around.

## Map

- [`ra-single-agent.md`](ra-single-agent.md): the entry point. Defines the shared primitive set `P1`–`P15`, the central claim that the workflow owns the process and the agent is a bounded step inside it, and the eight-element `AgentStep` boundary contract.
- [`ra-multi-agent.md`](ra-multi-agent.md): depends on the single-agent RA. Defines `P16`–`P18`, argues that multi-agent is a cost rather than a default, and catalogues the six topologies that cover the multi-agent shapes this RA recognises.
- [`primitives/`](primitives/): one file per primitive, `P1` through `P19`, each with its definition, the argument for why it exists, and its nearest equivalents across the engines and frameworks this WG has surveyed.
- [`contracts/`](contracts/): the two required contracts: the eight-element `AgentStep` boundary contract and the seven-element `Handoff` contract.
- [`topologies/`](topologies/): the six multi-agent shapes (`T1`–`T6`), each with a diagram, a selection rule, a characteristic failure mode, and where its deterministic boundary sits.
- [`guidance/`](guidance/): cross-cutting material both RAs draw on: loop tiers, determinism hints, the two checklists, anti-patterns, global invariants, failure modes, working-group boundaries, and the consolidated reference list.

## How to read this, if you're arriving with a use case

Start at [`guidance/checklist-single-agent.md`](guidance/checklist-single-agent.md), not at either RA's prose. The checklist will tell you, in four questions, whether your use case needs an `AgentStep` at all, and if it needs more than one.

From there:

1. If the checklist lands on "pure deterministic workflow" or "a single `AgentStep`," read [`ra-single-agent.md`](ra-single-agent.md)'s Purpose and Structure sections, then the [`AgentStep` boundary contract](contracts/agent-step-boundary.md) for the step itself.
2. If the checklist routes you to multi-agent, follow it to [`guidance/checklist-multi-agent.md`](guidance/checklist-multi-agent.md), which will very often route you straight back to step 1. That is its most common correct outcome, not a failure of the checklist.
3. If multi-agent is actually warranted, read [`ra-multi-agent.md`](ra-multi-agent.md)'s Purpose section for the four legitimate reasons to cross that line, then the [topology selection table](topologies/README.md#selection-table) to pick a shape, then the [handoff contract](contracts/handoff.md) for the coordination edges.
4. Whatever you land on, check [`guidance/anti-patterns.md`](guidance/anti-patterns.md) before building, and [`guidance/wg-boundaries.md`](guidance/wg-boundaries.md) if your workflow touches security, observability, governance, reliability, or identity concerns owned by another AAIF working group.

Everything in this RA set uses RFC 2119 keywords normatively, per BCP 14: `MUST`, `MUST NOT`, `SHOULD`, `SHOULD NOT`, and `MAY`, capitalized, mean exactly what those keywords mean there, and nowhere else in these documents.

## Related

- [Workstream README](../README.md)
- [Working Group Charter](../../../charter/charter.md)
- [Reference Architecture Proposal](../../../Workflows-and-Processes-Reference-Architecture-Proposal.md)

# Reconciliation Log

A running log of discrepancies found across the two RA files and this `docs/` folder, and how each was dispositioned. Closed items are retained rather than deleted, so the record itself is auditable.

Items 1 and 5 below were found during the original landscape survey's first pass and have since been corrected in the RA files. Item 2 was also corrected once already, but the original record of that correction was itself wrong; it is restated accurately below. Items 3 and 4 are non-substantive or resolved by context, not by edit. Items 6 through 8 were added while decomposing this survey into the focused files now under `docs/`.

## 1. `P14`'s classification vocabulary was asymmetric between the two RA files — resolved

`ra-multi-agent.md` stated the seven-value set explicitly while `ra-single-agent.md` — the document that defines `P14` — did not enumerate it. The set (`completed`, `completed_with_exceptions`, `escalated`, `rejected_by_policy`, `budget_exhausted`, `failed`, `cancelled`) is now canonical in `ra-single-agent.md`'s `P14` row, with `ra-multi-agent.md` referencing rather than restating it.

## 2. Working-group naming — corrected twice; one item still open

This entry itself needed correcting. An earlier pass recorded the disposition as "Both files now use the charter's own names — 'Security WG', 'Observability WG', 'Governance & Risk WG', 'Identity & Trust WG'" and marked the item resolved on that basis. That statement was wrong on its own terms: the two RA files did not, in fact, already agree even on those informal short forms. The actual sequence was: `ra-single-agent.md` and `ra-multi-agent.md` initially disagreed with each other on working-group naming; both were then briefly changed to the charter's own informal short forms (the wording the earlier pass described); and both have since been corrected a second time to the official working-group names listed in the repo root README ([`../../../README.md`](../../../README.md)), which is authoritative here — not the charter's informal usage, and not either RA file's prior wording:

- Accuracy & Reliability
- Agentic Commerce
- Governance, Risk & Regulatory Alignment
- Identity & Trust
- Observability & Traceability
- Security & Privacy
- Workflows & Process Integration

Both RA files now use the full official names with a `WG` suffix — "Security & Privacy WG," "Observability & Traceability WG," "Governance, Risk & Regulatory Alignment WG," "Accuracy & Reliability WG," "Identity & Trust WG" — confirmed directly in `ra-single-agent.md`'s and `ra-multi-agent.md`'s own boundaries sections.

One item remains open and belongs to the charter rather than to either RA file or to this folder: the charter is internally inconsistent about the Accuracy & Reliability WG's own name — it uses "Reliability & Accuracy Working Group" in §8 and "Accuracy and Reliability Working Group" in §3, two different orderings in the same document, while the root README says "Accuracy & Reliability." The RA files use "Accuracy & Reliability WG" for both, matching the root README. The WG should amend the charter to match the root README's naming rather than leave two different orderings live in the same document.

## 3. Cosmetic framing differences — not substantive

`ra-single-agent.md` carries an explicit author byline ("Praneeth Patil (Equinix)") and phrases its verification caveat as "Facts marked `> **[verify]**` need source confirmation before publication." `ra-multi-agent.md` has no author byline and phrases the equivalent caveat as "Passages marked `> **[verify]**` note claims that need source confirmation before this document is published; treat them as flagged, not as settled fact." Not substantive, but worth normalizing if the two documents are meant to read as one paired set.

## 4. The prior proposal is more prescriptive about BPMN extension than the RA files that supersede it — not an error

The proposal (§7, §11) recommends the WG "standardize an 'agent task' type" as a new BPMN extension. The RA files decline to take that position: `ra-single-agent.md`'s open questions explicitly leave open "how much of the `AgentStep` boundary contract can be expressed in BPMN `extensionElements`... versus requiring a new BPMN element altogether." This reads as a considered evolution — the proposal is dated 2026-07-27, ten days before the RA files — rather than an error, but it means a reader tracing the lineage from proposal to RA should expect the RA files to be more conservative about new BPMN notation than the document they are built on, not treat that as a contradiction to reconcile.

## 5. Sourcing rigor differed between `ra-single-agent.md` and this survey's own standard — resolved

`ra-single-agent.md` cited the MCP tool annotation names as settled fact in the tool-scope element of the `AgentStep` contract. Those names were not confirmed against the specification text for this draft, and the passage now carries a `> **[verify]**` marker consistent with this survey's own treatment (see [`landscape/interop-protocols.md`](landscape/interop-protocols.md)). The substantive argument — that the workflow, not the tool server, is authoritative on effect class — does not depend on the annotation names and stands either way.

## 6. Section 3, "Primitive-by-primitive justification," retired from `docs/`

This survey's original section 3 — a justification table for all eighteen primitives then defined, plus six expanded subsections (`P13`, `P14`, `P4`'s effect classification, `P16` versus `P5`, `P18`, `P11`) — has been retired from this folder. Each primitive's justification now lives with the primitive itself, in its own file under [`../ra/primitives/`](../ra/primitives/README.md), each carrying its own "Why it exists" section. This folder does not carry, and will not re-create, a standalone primitive-justification document. Cross-references elsewhere in this folder that used to point at "section 3" now point at the relevant primitive's own file, indexed at `../ra/primitives/README.md`.

## 7. Scope expansion: `P19 EvaluationGate` and the loop-tier model

After this landscape survey was first drafted, a nineteenth primitive was added to the primitive set: `P19 EvaluationGate` — a machine-checkable predicate an iterating `AgentStep` evaluates its own output against, to decide whether to iterate again or terminate. It shipped together with [`../ra/guidance/loop-tiers.md`](../ra/guidance/loop-tiers.md), a guidance document modelling four nested loops at different cadences. Both were prompted by the "loop engineering" framing in Andrew Ng's *The Batch* letter on the three loops for building 0-to-1 products.

Two notes on disposition:

- `P19` deliberately declares only the *slot* for a predicate, not the predicate itself. Evaluation quality — what makes a good eval, how gate quality is measured, how reasoning quality is assessed — belongs to the **Accuracy & Reliability WG**, not to this RA. This mirrors how the `AgentStep` boundary contract handles identity: the RA requires the attribute exist and be declared; another WG owns the mechanism.
- This `docs/` folder has not yet been updated with a landscape survey of iteration and eval tooling, comparable to the survey in [`landscape/`](landscape/README.md) for the other primitives. That is a follow-up, not something this decomposition pass attempted to backfill.

> **[verify]** The exact issue number, date, and URL of Andrew Ng's *The Batch* letter on the three loops has not been confirmed. Confirm before external publication.

## 8. A `[verify]` marker was lost when section 3's `P4` argument moved into `ra/primitives/p04-agent-step.md` — resolved

The retired section 3 (item 6, above) carried an inline marker on its illustrative example for why effect classification must be workflow-level, not server-level: a misconfigured or malicious MCP server could annotate an `irreversible` action as `readOnlyHint: true`, flagged `> **[verify]**` because the specific annotation field name was unconfirmed. When that argument moved into [`../ra/primitives/p04-agent-step.md`](../ra/primitives/p04-agent-step.md)'s "Effect classification is a workflow-level concern" section, the example was rephrased to "annotate an `irreversible` action as read-only," and both the specific field name and the marker were dropped.

Disposition: **resolved.** A marker was restored to [`../ra/primitives/p04-agent-step.md`](../ra/primitives/p04-agent-step.md)'s effect-classification section, cross-referencing the equivalent markers in [`../ra/primitives/p05-tool-call.md`](../ra/primitives/p05-tool-call.md) and [`../ra/contracts/agent-step-boundary.md`](../ra/contracts/agent-step-boundary.md), where the same annotation-name caveat survived the decomposition intact.

Worth recording as a process observation rather than just a fixed defect: the marker was lost because the argument it qualified was *rephrased* during the move — the specific field name was generalised to "read-only," which made the marker look redundant to whoever was moving it. Decomposition passes that rewrite prose are exactly where sourcing caveats go missing, and a marker count before and after is a cheap check worth repeating on any future split.

## Consistency check retained from the original survey

No inconsistency was found in the primitive IDs, primitive names, or obligation levels (MUST/SHOULD) themselves across the two RA files at the time of the original check: `P1`–`P15`'s table in `ra-single-agent.md` and `P16`–`P18`'s treatment in `ra-multi-agent.md` were mutually consistent everywhere checked, including the topology catalog's use of every primitive and the global invariants' references back to specific primitive obligations. This check predates `P19`'s addition (item 7, above) and has not been re-run against it.

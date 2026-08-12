# Vendor-Neutral Spec Efforts

CNCF Serverless Workflow: kept separate from the rest of the [landscape survey](README.md) because it is the closest existing precedent to what this WG is attempting, and is expected to grow as a point of comparison as the WG's own artifact develops. See [`../references.md`](../references.md) for sourcing.

## CNCF Serverless Workflow

CNCF Serverless Workflow (see [CNCF Serverless Workflow](https://serverlessworkflow.io/)) is a vendor-neutral workflow definition language proposed under the Cloud Native Computing Foundation, itself, like AAIF, a Linux Foundation project, which is what makes it the most direct precedent for what this WG is attempting.

> **[verify]** This document does not have session-confirmed detail on Serverless Workflow's current specification version, adoption footprint, or implementation ecosystem, and does not assert those specifics as fact.

The precedent is worth taking seriously as both an ally and a cautionary tale. As an ally: it demonstrates that a vendor-neutral DSL for workflow orchestration, governed under the same broad foundation family this WG operates in, is a viable thing to attempt. It is not a hypothetical governance model. As a cautionary tale: `ra-multi-agent.md`'s interop-layer section already names Serverless Workflow, alongside Temporal, Dapr Agents, LangGraph, n8n, and AWS Step Functions, as one of the ecosystems with "no shared, portable way to express a cross-agent budget ceiling or a deterministic arbitration rule": meaning a prior vendor-neutral workflow specification effort, operating in exactly this WG's problem space, has not itself closed the `P13`/`P18` gap this RA identifies as its sharpest contribution. Whatever adoption lesson explains that gap (a specification that does not solve a problem practitioners are not yet demanding it solve, or one that solved a narrower problem than agentic budget/arbitration) is directly relevant to whether this WG's own artifact gets used rather than merely published, and is worth the WG researching directly rather than assuming away.

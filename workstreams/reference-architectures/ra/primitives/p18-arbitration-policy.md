# P18 ArbitrationPolicy

**Obligation:** MUST when agents can disagree
**Introduced by:** [Multi-agent workflow RA](../ra-multi-agent.md)

## Definition

A deterministic rule, evaluated outside any agent's own reasoning, that resolves disagreement between agent outputs and produces an outcome regardless of whether the disputing agents would themselves ever agree.

## Why it exists

Arbitration must be deterministic because the alternative — letting agents resolve their own disagreement through further negotiation — does not actually terminate the disagreement, it relocates it. The multi-agent RA's global invariants state this plainly: if two agents disagree, adding a third agent to mediate does not produce a deterministic resolution — it produces a more complex negotiation with the same unbounded-disagreement problem one level up.

This is a termination argument, not merely a predictability preference. Every multi-agent topology MUST have a deterministic path to a `P14 OutcomeContract` classification that does not depend on any agent choosing to stop, and a disagreement resolved by agent negotiation has no such path by construction — negotiation between two reasoning systems has no guaranteed halting point unless something outside both of them imposes one. `P18` is that something: a rule stated in advance, evaluated outside any agent's own reasoning, that produces an outcome regardless of whether the disputing agents would themselves ever agree.

DMN decision tables are the natural mechanism for expressing the rule *inside* `P18` where the rule is genuinely a bounded decision — a fixed set of conditions mapping to a fixed set of resolutions. But the primitive itself is broader than any single notation for expressing its rule, and no cross-runtime, cross-agent representation of an arbitration rule exists today across any of the specifications or runtimes this RA builds on. `P18` names the requirement; it does not yet have a portable expression, which is a distinct, standing gap rather than something this document resolves.

### Where P18 is required

Three topologies in the multi-agent RA's catalog name `P18` explicitly:

- **Concurrent fan-out with deterministic join (T3).** The join MUST declare, in advance, what it does with `k` of `n` results — proceed once a quorum returns, wait for all `n`, or fail if fewer than `k` return. `P18` governs the case where the results that do return disagree with one another, not merely the case where some are missing.
- **Shared-context / blackboard (T5).** `P18` resolves conflicting writes to `P17 SharedContext` — two agents that read the same state, act on it independently, and write back results that silently overwrite one another.
- **Generator–critic / evaluator-optimizer (T6).** `P13 Budget` bounds the iteration count; `P18` is the deterministic stopping/acceptance rule.

### The critic must not be the arbiter

The generator–critic topology is the sharpest illustration of why `P18` cannot be folded back into one of the agents it governs: **the critic MUST NOT also be the arbiter.** If the same agent both critiques the work and decides when critique is "good enough," there is no independent check left in the loop, and the loop's termination depends entirely on that one agent's judgement rather than on a declared rule. A generator–critic loop with no cap and no independent arbiter will iterate for as long as the critic can find something to critique, which for an LLM critic is close to indefinitely — this is the "unbounded critique loop" failure mode, and the fix is a `P13` iteration cap paired with a `P18` stopping rule evaluated outside the critic's own reasoning.

### Failure mode this primitive prevents

| Failure mode | How it manifests | What prevents it |
| --- | --- | --- |
| Disagreement with no arbiter | Two agents produce conflicting outputs and the workflow has no rule to resolve the conflict, so it stalls or picks arbitrarily | `P18`, deterministic and mandatory when agents can disagree |
| Conflicting concurrent writes to `P17` | Two agents read the same shared state, act independently, and overwrite each other's result without either noticing | `P17`'s declared consistency model, plus `P18` for the conflict itself |
| Unbounded critique loop | A generator–critic loop never converges because the critic can always find something more to critique | A `P13` iteration cap plus a `P18` deterministic stopping rule |

### Anti-patterns this primitive exists to prevent

- **Agents negotiating instead of a declared `P18 ArbitrationPolicy`.** Consequence: disagreement is unbounded and the workflow has no guaranteed path to termination.
- **A critic that is also the arbiter.** Consequence: the generator–critic loop has no independent check left in it, and its termination depends entirely on one agent's judgement rather than a declared rule.

### A design checklist item, not an afterthought

The multi-agent RA's own checklist places this before any implementation work begins: name the workflow's `P18 ArbitrationPolicy` — the deterministic rule that resolves disagreement, even if disagreement is expected to be rare — before writing any code. If agents in a workflow can disagree and no arbitration policy can be named, the design is not ready. The corresponding anti-pattern is agents negotiating instead of a declared policy, with the consequence that disagreement is unbounded and the workflow has no guaranteed path to termination.

### What makes a DMN decision table a fit for the rule inside P18

DMN separates decision logic from process flow: a Decision Requirements Diagram shows which decisions depend on which other decisions and which input data, and each decision node's logic is typically expressed as a decision table — a set of rules, each mapping input conditions to an output, disambiguated by a declared hit policy (for example, first-match, unique-match, or collect-all-matches) when more than one rule could otherwise fire. FEEL, DMN's expression language, is designed to be readable by a business analyst rather than only a developer. This gives `P18` a reviewable, testable, versionable way to express the rule *inside* the policy wherever that rule is genuinely a bounded decision — but it does not close the gap this primitive names: DMN has no notion of an *agent* as one of the parties whose disagreement it resolves, and no cross-runtime way to carry that decision table's result as a first-class arbitration outcome across a workflow engine, an MCP-tool-using agent, and an A2A-delegated peer alike.

## Prior art

| System | Nearest equivalent |
| --- | --- |
| DMN | Decision tables — the natural mechanism for the deterministic rule inside a `P18` policy, where the rule is a bounded decision |

Nearest equivalent is editorial judgment, not a conformance claim. No cross-runtime, cross-agent representation of a `P18` arbitration policy exists today across any of the specifications or runtimes surveyed — BPMN 2.0, n8n, AWS Step Functions, Temporal, and LangGraph all lack a first-class construct for a deterministic rule that resolves disagreement between independent agents. This absence is itself one of the sharper gaps named across the source material this RA draws on.

## Relationship to other primitives

- [P17 SharedContext](p17-shared-context.md) — required when concurrent writes to shared state can conflict.
- [P7 FanOut/FanIn](p07-fan-out-fan-in.md) — required when fanned-out branches' outputs can conflict.
- [P13 Budget](p13-budget.md) — a generator–critic loop needs both a hard iteration cap and a deterministic stopping rule; `P18` is the rule.
- [P14 OutcomeContract](p14-outcome-contract.md) — the classification an arbitrated conflict resolves to.
- [P4 AgentStep](p04-agent-step.md) — in the supervisor/orchestrator-worker topology, `P18` resolves conflicts between worker outputs at the deterministic join, regardless of whether the supervisor itself is an agent or a deterministic component.
- [P16 Handoff](p16-handoff.md) — a chain of handoffs that could produce conflicting claims on a shared goal still needs exactly one arbitration point, not one per hop.

## Open questions

- What would a portable, cross-runtime representation of `P13 Budget` and `P18 ArbitrationPolicy` actually look like in a concrete schema, and should it be proposed as an extension to an existing standard rather than as new, WG-owned notation?
- Is a single WG-standard vocabulary of arbitration outcomes (accept, reject, escalate, defer) worth specifying centrally, or should each workflow continue to define its own resolution vocabulary the way `P17`'s consistency model is left per-workflow?


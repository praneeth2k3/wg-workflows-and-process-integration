# Single-Agent Process with Human Approval

**The job it serves:** Human-approved operation: an agent prepares an action, a named person approves or rejects it, and only then may the protected effect occur \
**Built from patterns:** [Proposal/execution split](../patterns/proposal-execution-split.md) · [Human approval gate](../patterns/human-approval-gate.md) · [Durable wait](../patterns/durable-wait.md) \
**Requires capabilities:** Execution engine · agent · tool access layer · state/context store · human interaction surface · workflow-controlled executor · audit log \
**Audience:** Engineers building agentic workflows · architects reviewing designs · the approvers and risk owners related to the gate this architecture implements

## Status

Draft for working group review. Nothing here is locked.

## Purpose

The reference architecture for a common enterprise job: **an agent prepares
an action, a human approves it, the system executes it.**

Use when an agent's output can change real systems — a refund, a
configuration change, an outbound message — and the enterprise needs
enforceable behavior at the point of effect: because the effect is costly or
hard to reverse, because audit obligations require proving exactly what a
person authorized, or because a named person must own the decision.

This architecture puts a human-mediated structural break between the agent's
proposal and the protected effect. The agent may investigate, calculate, and
construct a proposal, but it cannot invoke the capability that causes that
effect. A human reviews the exact proposal, and a separate,
workflow-controlled executor acts only after recording explicit approval.

Built to the invariants in its composed patterns, this architecture guarantees:

- **No protected effect occurs without human approval** — and what executes
  is exactly what was approved, not a revision of it.
- **The run survives the wait.** Approvals take hours or days; no restart,
  deploy, or crash loses a pending request or repeats a completed action.
- **Every run ends in a known state**, with the full intent → decision →
  effect chain — proposer, approver, executor — in the system of record.

**Reading paths:** For the *what* and the *why*, read this section and the
illustrative refund walkthrough — together they carry the architecture in
practice. Every other section is implementation guidance for readers building
or reviewing this architecture for themselves.

## Is this your architecture? (checklist)

Bring a use case, walk the questions:

1. **Does the reasoning work require more than one independent agent?** Agent
   count is a design outcome, not a use-case property —
   default to one agent and let the workflow provide the structure. Route
   to a future job-oriented multi-agent architecture only on concrete signals:
   independent subtasks that must run in parallel, duties that policy requires
   be split across separate identities, or coordination across workflows owned
   by different teams. No multi-agent architecture is currently defined.
   "The task is big" is not a signal — capacity is an implementation concern,
   and it shifts with every model generation.
2. **Can any activity cause a protected effect that needs human sign-off?** If yes,
   and approve/reject (with optional feedback) is the interaction you need —
   this is your architecture; keep going. If the human's role is richer than
   a gate (co-editing the draft, steering the agent mid-task, supervising as
   it acts), that is a different human-in-the-loop flow this architecture
   does not cover — don't force it into a gate. If authorization comes from an
   automated policy rather than a person, that is a separate architecture with
   a different attribution and failure topology. If no approval is required,
   this is not your architecture.
3. **Will the approval take longer than a process lives?** Almost always yes
   (humans answer in hours, not milliseconds) — which is why the
   [durable wait](../patterns/durable-wait.md) is a required pattern here,
   not an option.
4. **Can you separate the activities that must carry guarantees or attribution
   from the activities that are open-ended?** Activities with guarantee requirements —
   validated inputs, the attributed decision, the idempotent execution of
   the approved action — belong outside the agent boundary as
   workflow-controlled activities, where guarantees can actually be enforced. The
   open-ended
   activities (interpreting the request, drafting the proposal) are what the
   agent is for. If you can't yet draw that line, draw it before choosing
   any architecture — the agent and protected-effect boundary in *Structure*
   is that line, drawn for this job.

The same checklist works in reverse to assess an existing workflow: check it
against each invariant, exit state, and boundary below — the gaps are your
findings.

## Variants

No variants are currently defined. Two nearby flows are deliberately not
variants of this architecture:

- **Policy-authorized operation** replaces a named human decision with a
  versioned policy evaluation. Its attribution evidence, failure states, and
  timing differ, so it requires a separate architecture.
- **Straight-through automation** removes the defining human-approval
  guarantee. It serves a different job and requires its own risk boundary.

A durable wait on a timer or external callback is the
[durable wait](../patterns/durable-wait.md) pattern used in another
composition, not a variant of this architecture.

## Structure

Component-and-relationship level only. The agent's internal reasoning loop is
deliberately unspecified — deterministic and ReAct-style internals are equal
implementation choices inside the boundary.

```mermaid
flowchart TB
    E["Execution engine<br/>activity ordering · retries · timers · waits"]

    subgraph AB["Agent boundary"]
        A["Agent<br/>internal loop unspecified"]
        T["Tool / capability access layer<br/>proposal support only"]
        A --> |"tool call"| T
        T --> |"result / error"| A
    end

    I["Information sources and draft or staging stores<br/>cannot cause the protected effect"]
    H["Human interaction surface<br/>approve · reject · escalate"]
    X["Workflow-controlled executor<br/>validate · execute"]
    P["Protected target system"]
    S[("State / context store")]
    R[("Audit log / system of record")]

    E --> |"task"| A
    A --> |"proposal / failure / escalation"| E
    T --> |"read / draft / stage"| I
    I --> |"result"| T
    E --> |"pending approval"| H
    H --> |"attributed decision event"| E
    E --> |"approved proposal"| X
    X --> |"protected effect"| P
    E --> |"resumable run record"| S
    A --> |"proposal event"| R
    H --> |"decision event"| R
    X --> |"execution outcome"| R
```

### What crosses each arrow

Names, not formats: these say what crosses each boundary and the rule that
governs it there. Field-level definitions (formats, event shapes, binding
semantics) are deliberately left out — that is future interoperability work
(charter 3E), best done after the pattern entries accumulate more examples
of how real runtimes handle these boundaries.

| Arrow | What crosses | Invariant at this boundary | Primitives |
|---|---|---|---|
| Engine ⇄ agent | Task in; exactly one of proposal / failure / escalate out | The agent cannot cause the protected effect | `P3` |
| Engine ⇄ human surface | Pending approval (proposal reference + deadline) in; decision event (approve/reject, who, when) out | The decision binds to an immutable proposal version; no default-approve | `P5` · `P6` |
| Agent ⇄ tool / capability access layer | Tool call; result or error | Tools may support investigation and proposal construction but cannot expose the capability or credentials that cause the protected effect | `P4` |
| Engine ⇄ workflow-controlled executor | Approved proposal in; execution outcome out | Only the exact approved proposal may reach the protected target; retries are idempotent | `P2` · `P7` |
| Engine → state store | Run record (state + position, fully resumable) | While parked, the record *is* the run | `P9` |
| All → audit log | Event record (run id, activity, outcome) | One run identity joins intent, decision, and effect | `P12` |

The required capabilities and their responsibilities:

| Capability | Responsibility in this architecture | Charter basis | Hosts primitives |
|---|---|---|---|
| Execution engine / orchestrator | Owns activity ordering, retries, timers, and waits; invokes the agent as one activity among others | 3A | `P1` · `P6` · `P7` · `P8` · `P10` · `P11` |
| Agent | Performs the reasoning-heavy activity or activities; internals unspecified | 3A | `P3` |
| Tool / capability access layer | Supports investigation and proposal construction without exposing the protected-effect capability | 3C | `P4` |
| State / context store | Persists workflow state so runs can pause and resume | 3B | `P9` |
| Audit log / system of record | Records execution history, activity outcomes, decision points — distinct from Observability WG's traces/metrics | 3B | `P12` |
| Human interaction surface | Presents pending approvals; captures approve/reject/escalate | 3D | `P5` |
| Workflow-controlled executor | Validates and executes the exact approved proposal using capabilities unavailable to the agent | 3A, 3F | `P2` |

These are capability roles, not a prescribed deployment topology. An
implementation may realize each as a separate technical component or combine
several in one platform.

### Primitives

Capabilities say *what components exist*; primitives say *what a workflow
definition is written in*. Primitives are the building blocks an implementer
declares in an engine, a workflow-as-code platform, or an agent framework.
The last column of the capability table shows which component hosts each one.
Every primitive has its own file in [`primitives/`](../primitives/README.md)
with its definition, rationale, the patterns that use it, and how runtimes
implement it. The table below is only an index. The `P` IDs in the rest of
this document refer to it, and the [appendix](#appendix-primitive-mapping)
draws the whole run with them.

| ID | Primitive | Obligation | Its job in this architecture |
|---|---|---|---|
| P1 | [Trigger](../primitives/p01-trigger.md) | MUST | Starts exactly one run per request and fixes the run identity |
| P2 | [DeterministicTask](../primitives/p02-deterministic-task.md) | MUST | Input validation, the workflow-controlled executor, close-out |
| P3 | [AgentTask](../primitives/p03-agent-task.md) | MUST | The bounded, contracted task that drafts the proposal |
| P4 | [ToolCall](../primitives/p04-tool-call.md) | MUST | Agent tool use, limited to read, draft, and stage |
| P5 | [HumanCheckpoint](../primitives/p05-human-checkpoint.md) | MUST | The approval gate |
| P6 | [TimerDeadline](../primitives/p06-timer-deadline.md) | MUST | Approval deadline and, if chosen, approval validity window |
| P7 | [ErrorBoundary](../primitives/p07-error-boundary.md) | MUST | Typed failure capture and orchestrator-owned retries |
| P8 | [Escalation](../primitives/p08-escalation.md) | MUST | Approver escalation, "cannot proceed", deadline expiry |
| P9 | [StateCommit](../primitives/p09-state-commit.md) | MUST | The parked run record; decision recorded before resume |
| P10 | [Budget](../primitives/p10-budget.md) | MUST | Agent ceilings, rejection-loop cap, executor retry budget |
| P11 | [OutcomeContract](../primitives/p11-outcome-contract.md) | MUST | Exactly one recorded exit state per run |
| P12 | [AuditRecord](../primitives/p12-audit-record.md) | MUST | The intent → decision → effect chain under one run identity |

## Pattern composition

Per-pattern invariants, failure modes, and runtime examples live in the
pattern entries — this table records where each pattern sits in *this*
composition and why it is required:

| Pattern | Where it sits | Why it is required here | Built from primitives |
|---|---|---|---|
| [Proposal/execution split](../patterns/proposal-execution-split.md) | Between the agent's proposal and the protected effect | Probabilistic intent must not directly cause the governed effect; authorization must bind to exactly what executes | `P3` (proposal as output) · `P4` (scope excludes the effect) · `P5` (authorization) · `P2` (executor) · `P12` |
| [Human approval gate](../patterns/human-approval-gate.md) | At the authorization decision | The decision must be made by an identified, authorized person and recorded with the proposal they reviewed | `P5` · `P6` · `P8` · `P9` · `P12` |
| [Durable wait](../patterns/durable-wait.md) | At the approval gate | Approvals take hours or days; the run must outlive every process that hosts it | `P9` · `P6`; resumed by the `P5` decision event |
| Idempotent executor *(candidate — entry not yet written)* | The execution activity | Retries after approval must not repeat the effect | `P2` · `P7` · `P10` (retry budget) |

**Invalid composition: the one shortcut that breaks everything:** the agent
can invoke the protected-effect capability directly or obtain the executor's
credentials. In primitive terms, the protected effect becomes a `P4` tool
call instead of a `P2` task after the `P5` gate. That is not a leaner variant
of this architecture; it removes
the gate's entire value, because approval can be bypassed and review happens
after the risk instead of before it.

## The agent and protected-effect boundary

What the agent is allowed to do:

- Interpret the goal and produce a plan, draft, or proposed action (`P3`)
- Call tools for investigation, calculation, and proposal construction,
  including writes to draft or staging stores that cannot cause the protected
  effect (`P4`)
- Signal completion, failure, or "cannot proceed — escalate" (`P3` output
  contract, leading to `P11` or `P8`)

What must be handled outside the agent:

- Triggering and input validation (`P1`, `P2`)
- Possession and invocation of the protected-effect capability (`P2`)
- Retries and idempotency of protected-effect calls (`P7`, `P10`)
- Enforcing the agent's own budget (`P10`)
- Recording run state and resuming from it (`P9`)
- The human gate and escalation path (`P5`, `P6`, `P8`)
- Validation and final execution of approved actions (`P2`)
- Audit logging (`P12`)

The eight-element contract every agent task declares is in
[P3 AgentTask](../primitives/p03-agent-task.md).

## Choosing deterministic and model-driven activities

- Side-effecting execution is a rule-based, workflow-controlled activity (`P2`)
  *after* the gate, not an agent action. The agent's role ends at the proposal;
  execution belongs to the workflow.
- Retries around the agent activity need idempotency at the workflow level — an
  agent activity re-run is not guaranteed to produce the same output. Retries
  are declared at a `P7` ErrorBoundary and spend against a `P10` Budget.
- Use conventional workflow logic for lookups, transforms, validation, and
  templates when their rules can be stated explicitly (`P2`). Use model
  reasoning (`P3`) where the input is open-ended and interpretation or
  judgment is required.

## Exit states

Every way a run can end — each lands in the system of record. A run that can
end in a state not listed here is a bug in this architecture, not in the
implementation.

Each run records exactly one of these as its `P11` OutcomeContract, in a
`P12` AuditRecord.

| Exit state | Reached when | Recorded as | Produced by |
|---|---|---|---|
| Completed | Approved proposal executed successfully | Full intent → decision → effect chain | `P2` executor succeeds |
| Rejected | Gate declines; feedback loop (if any) exhausted its iteration cap | Decision + rationale, per iteration | `P5` reject, or `P10` loop cap |
| Expired | Approval timeout with no decision; escalation path also exhausted or declined | Timeout + escalation trail | `P6` deadline, then `P8` exhausted |
| Cancelled | An authorized requester or operator withdraws the run before the protected effect begins | Cancelling principal, reason, proposal version, and last run state | No primitive yet (see open questions) |
| Escalated (handed off) | Run leaves this workflow for a human-owned process | Handoff target + state at handoff | `P8` |
| Failed | Agent cannot produce a proposal, or execution fails past its retry budget | Failure class + last recorded state | `P7` unresolved, or a `P10` ceiling |

## Illustrative worked scenario: the refund

This walkthrough makes the architecture concrete but is not yet sourced
validation from the Use Cases workstream: *an agent drafts a customer refund,
finance approves it, and the ERP executes it.*

1. **Trigger** (`P1`, `P2`) — a refund request arrives; a deterministic activity validates it
   and the engine starts a run (audit: run started).
2. **Agent drafts** (`P3`, `P4`, `P10`) — the agent activity reads the order history and policy
   through the tool access layer and produces a refund proposal — amount,
   justification, target account (audit: proposal + rationale).
3. **Durable wait** (`P9`, `P6`) — the engine records the run's state and parks it at the
   gate. Finance answers in hours or days; the run survives restarts.
4. **Finance decides** (`P5`, `P8`) — the interaction surface presents the proposal.
   Approve → continue; reject → terminate or loop back with feedback;
   timeout → escalate (audit: decision, who, when).
5. **ERP executes** (`P2`, `P7`) — the workflow-controlled executor performs the approved
   refund with retries and idempotency keys. The agent is not involved.
6. **Close out** (`P11`, `P12`) — outcome recorded; the system of record holds the full
   intent → approval → execution chain.

Every activity also appends a `P12` AuditRecord under the run identity `P1`
established.

### The run over time — where durability lives

The component diagram can't show the property that makes this durable: the
run outlives any process that hosts it. Same scenario, lifecycle view:

```text
◄──────────── process lifetime 1 ────────────►    ◄──────── process lifetime 2 ────────►

trigger → validate → agent drafts → record run    approval event → reload run from
                                    → run PARKED  state store → record decision →
                                          ⏸       resume at gate → execute → close out
─────────────────────────────────────────────────────────────────────────────────────────
                                    └── hours or days pass; processes die and
                                        deploys happen — the run is not lost ──┘
```

### If the process dies mid-run

| If the process dies during… | What happens on restart | What makes it safe | Primitive that carries it |
|---|---|---|---|
| 1. Trigger / validate | The trigger redelivers; validation re-runs | Validation is deterministic and side-effect-free | `P1` dedup · `P2` |
| 2. Agent drafts | Engine re-runs the agent activity from the last recorded state | Nothing has executed yet; a different draft is acceptable — the gate still stands between draft and effect | `P9` · `P7` within `P10` |
| 3. Durable wait | Run reloads as parked, still waiting | The record *is* the run; no process memory is involved | `P9` · `P6` |
| 4. Human decides | Decision is recorded before resume; redelivery is deduplicated | Resume is idempotent — the same approval delivered twice executes once | `P5` · `P9` |
| 5. System executes | Execution activity retries | Idempotency keys on the side-effecting call: retried, not repeated | `P2` · `P7` |
| 6. Close out | Audit write retries | Audit append is idempotent per run and activity | `P12` |

## Composition considerations

Concerns that only appear when the patterns combine — per-pattern invariants
live in the pattern entries:

- **Approval staleness.** The durable wait plus the proposal/execution
  split means the world can change between proposal and execution: a refund
  approved on Monday may exceed the customer's remaining balance by
  Thursday. Either the executor re-validates preconditions before acting
  (`P2`), or approvals carry an expiry (`P6`) — one of the two, explicitly.
- **Bounded rejection loops.** Reject-with-feedback re-enters the agent
  activity. Without an iteration cap (`P10`) and an exit to escalation
  (`P8`), a strict reviewer plus a stubborn agent is an infinite loop with an
  LLM bill. Total agent spend per run is bounded by the per-invocation budget
  times the loop cap plus one.
- **Proposal supersession.** A revised proposal is an in-run transition, not
  a terminal state. It invalidates any approval attached to the previous
  version and returns the run to pending approval. If the implementation
  instead starts a new run, the original run ends as cancelled with a link to
  its replacement.
- **One chain of custody.** Proposal, wait/resume, human decision, and effect
  records must share a run identity (`P1`, carried by every `P12` record).
  Records that cannot be joined afterward fail the whole point of the gate.

## Out of scope (handled elsewhere)

- **Traces, metrics, and runtime telemetry** — Observability WG. This
  architecture's audit log is the system of record (execution history, activity
  outcomes, decision points), which the charter explicitly distinguishes
  from observability signals.
- **Agent identity, delegation, and authorization** — Identity & Trust WG.
  This architecture requires the protected-effect capability and executor
  credentials to remain unavailable to the agent; how identities and grants
  enforce that separation is theirs.
- **Quality of the proposal** — Reliability & Accuracy WG. The gate ensures
  a human reviews the proposal; it does not make the proposal good. Prompt
  engineering and reasoning-quality evaluation are out of scope for this WG
  entirely.

## Validation record

Sourced scenarios from the Use Cases workstream provide validation. Every
scenario walked through this architecture gets a row, fit or no-fit;
illustrative examples are labeled separately until a source exists.

| Scenario | Source | Result | Notes |
|---|---|---|---|
| The refund (agent drafts, finance approves, ERP executes) | Working group discussion; pending a Use Cases workstream entry | Illustrative only — validation pending | Exercises all components and three composed patterns |
| FAQ & knowledge base update | [Use Case Inventory — Customer support](../../critical-use-cases/use-case-inventory.md#customer-support) | Validated | The agent drafts knowledge-base content, and a person approves it before publication. |
| Release notes generation | [Use Case Inventory — SDLC](../../critical-use-cases/use-case-inventory.md#sdlc) | Validated | The agent drafts release notes, and a person approves them before publication. |
| Listing description generation | [Use Case Inventory — Real estate](../../critical-use-cases/use-case-inventory.md#real-estate) | Validated | The agent drafts listing copy, and a person approves it before publication. |

## Open questions

- Is *idempotent executor* its own pattern entry, or a clause of the
  proposal/execution split?
- Where exactly does the tool access layer end and the Identity & Trust
  WG's delegation concern begin?
- Does the audit log / system of record boundary with the Observability WG
  read the same to everyone? (Charter says yes; worth confirming once.)
- The primitive mapping leaves two exit states without a clean fit:
  `Expired` has no value in the proposed canonical termination
  classifications, and no primitive yet models the cancel request behind
  `Cancelled`. See [P11 OutcomeContract](../primitives/p11-outcome-contract.md).

## Appendix: Primitive mapping

The sections above tag each part of the architecture with its
[primitives](#primitives). The diagram below puts them together as one run, so
an implementer can see what to declare in their runtime. It adds no new
guarantees. Gaps found while mapping are listed as open questions in the
primitive files.

```mermaid
flowchart TB
    T["P1 Trigger<br/>request arrives"] --> V["P2 DeterministicTask<br/>validate input"]
    V --> A
    subgraph A["P3 AgentTask, bounded by P10 Budget"]
        TC["P4 ToolCall<br/>read · draft · stage only"]
    end
    A --> |"proposal"| C["P9 StateCommit<br/>park with proposal + deadline"]
    C --> H{"P5 HumanCheckpoint<br/>bounded by P6 TimerDeadline"}
    H --> |"approve"| X["P2 DeterministicTask<br/>workflow-controlled executor"]
    H --> |"reject with feedback<br/>(within P10 loop cap)"| A
    H --> |"escalate / deadline"| E["P8 Escalation"]
    E --> |"new approver"| H
    X --> O["P11 OutcomeContract<br/>exit state"]
    H --> |"reject"| O
    E --> |"handed off / exhausted"| O
    A --> |"cannot proceed"| E
    A -.->|"typed failure"| F["P7 ErrorBoundary"]
    X -.->|"typed failure"| F
    F --> O
```

Every node also emits a [P12 AuditRecord](../primitives/p12-audit-record.md)
event under the run identity `P1` establishes.

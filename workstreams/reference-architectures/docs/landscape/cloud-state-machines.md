# Cloud State Machines

AWS Step Functions, Azure Logic Apps, and Google Cloud Workflows: declarative, cloud-native state machines shipped independently by three vendors. Part of the [landscape survey](README.md); see [`../references.md`](../references.md) for sourcing.

## AWS Step Functions

AWS Step Functions (see [AWS Step Functions developer guide](https://docs.aws.amazon.com/step-functions/latest/dg/welcome.html)) executes state machines defined in the Amazon States Language, with state types including `Task` (invoke a unit of work, typically a Lambda function or a direct service integration), `Choice` (branch on a condition), `Parallel` (run a fixed set of branches concurrently), `Map` (run one branch over each element of a collection), `Pass` (pass input to output, optionally transformed), `Wait` (pause for a duration or until a timestamp), and the terminal `Succeed`/`Fail` states. Step Functions offers two execution modes: Standard, which is durable and auditable with a persisted execution history, and Express, tuned for high-volume, short-duration workloads. This is a distinction with no direct analogue elsewhere in this landscape. Its `.sync` integration pattern lets a `Task` block until a called service (for example, another state machine, or an AWS Batch job) completes, and its `.waitForTaskToken` pattern lets a `Task` pause until an external caller (commonly a human, via a callback) returns a task token, which is Step Functions' native human-in-the-loop mechanism. `Retry` and `Catch` fields on a state give typed, declarative error handling without a separate boundary-event notation.

**What Step Functions gives for free:** a first-class `Parallel`/`Map` construct (`P7 FanOut/FanIn`'s mechanism), a genuinely durable human-pause primitive via `.waitForTaskToken` (`P6 HumanCheckpoint`'s mechanism), and declarative `Retry`/`Catch` (`P9 ErrorBoundary`'s mechanism), all native to the state machine definition itself, with no engine extension required. **What it does not give:** no budget ceiling construct, no compensation handler (composed from `Catch` plus a hand-written compensating `Task`, per `ra-single-agent.md`'s cross-mapping table), and no dedicated agent-runtime concept: an agent invoked from a `Task` state is, from Step Functions' point of view, simply whatever that Task's target service does.

## Azure Logic Apps

Azure Logic Apps (see [Azure Logic Apps](https://learn.microsoft.com/en-us/azure/logic-apps/)) is Microsoft's visual, connector-based workflow service: a workflow is defined as a trigger followed by a sequence of actions, each drawn from a large catalog of first- and third-party connectors, with conditional branching and looping constructs available in the designer.

> **[verify]** This document does not have a verified, specific source for Logic Apps' current agentic-capability surface (whether it has an equivalent to an AI Agent node, and what its tool-attachment or human-pause constructs are specifically called), and does not assert those specifics as fact.

The architectural point that does not depend on those specifics: Logic Apps occupies the same connector-driven, low-code niche as n8n, Zapier, Make, and Workato (see [`low-code-orchestration.md`](low-code-orchestration.md)), a large existing population of workflow builders whose vocabulary this RA's primitive set has to remain legible to.

## Google Cloud Workflows

Google Cloud Workflows (see [Google Cloud Workflows](https://cloud.google.com/workflows/docs)) defines workflows as a sequence of steps in a YAML or JSON workflow-definition language, invoking HTTP endpoints and other Google Cloud services, with support for conditional branching, parallel steps, and error handling within that definition language.

> **[verify]** This document does not have a verified, specific source for Cloud Workflows' current agentic-capability surface and does not assert specifics about it as fact.

The architectural point again does not depend on the specifics: Cloud Workflows is a code-adjacent (YAML/JSON-defined) rather than graphically-modeled orchestrator, closer in spirit to Step Functions than to n8n or BPMN, and its inclusion here is to confirm that the "cloud state machine" category is not a two-vendor phenomenon: AWS, Microsoft, and Google have each shipped a broadly similar declarative-state-machine product independently.

# References

**Standards and foundations**

- OMG: [BPMN 2.0](https://www.omg.org/spec/BPMN/2.0/)
- OMG: [DMN](https://www.omg.org/spec/DMN/)
- OMG: [CMMN](https://www.omg.org/spec/CMMN/)
- [Model Context Protocol specification (2025-06-18)](https://modelcontextprotocol.io/specification/2025-06-18)
- [A2A Protocol specification](https://a2a-protocol.org/latest/)
- [CNCF Serverless Workflow](https://serverlessworkflow.io/)
- Linux Foundation: [AAIF formation](https://www.linuxfoundation.org/press/linux-foundation-announces-the-formation-of-the-agentic-ai-foundation)
- Linux Foundation: [A2A Protocol project](https://www.linuxfoundation.org/press/linux-foundation-launches-the-agent2agent-protocol-project-to-enable-secure-intelligent-communication-between-ai-agents)
- AAIF: [From Workflow Orchestration to Agentic Orchestration](https://aaif.io/blog/from-workflow-orchestration-to-agentic-orchestration)
- [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework)
- [OWASP Agentic Applications Top 10](https://owasp.org/www-project-agentic-skills-top-10/)

**Engines, frameworks, and vendor documentation**

- Camunda: [Ad-hoc sub-processes](https://docs.camunda.io/docs/components/modeler/bpmn/ad-hoc-subprocesses/)
- Camunda: [AI Agent Sub-process connector](https://docs.camunda.io/docs/components/connectors/out-of-the-box-connectors/agentic-ai-aiagent-subprocess)
- Camunda: [Agentic orchestration overview](https://docs.camunda.io/docs/components/agentic-orchestration/agentic-orchestration-overview/)
- Camunda: [Essential agentic patterns for AI agents in BPMN](https://camunda.com/blog/2025/03/essential-agentic-patterns-ai-agents-bpmn/)
- Camunda: [AI agent or rule-based DMN?](https://camunda.com/blog/2025/07/ai-agent-or-based-rule-dmn-ai-powered-orchestration/)
- Camunda: [Guardrails and best practices for agentic orchestration](https://camunda.com/blog/2026/01/guardrails-and-best-practices-for-agentic-orchestration/)
- Anthropic: [Building Effective Agents](https://www.anthropic.com/research/building-effective-agents)
- [Temporal documentation](https://docs.temporal.io/)
- [Dapr Workflow](https://docs.dapr.io/developing-applications/building-blocks/workflow/workflow-overview/)
- [Dapr Agents](https://dapr.github.io/dapr-agents/)
- [LangGraph documentation](https://langchain-ai.github.io/langgraph/)
- [CrewAI documentation](https://docs.crewai.com/)
- [Microsoft AutoGen](https://microsoft.github.io/autogen/)
- [OpenAI Agents SDK](https://openai.github.io/openai-agents-python/)
- [n8n documentation](https://docs.n8n.io/)
- [AWS Step Functions developer guide](https://docs.aws.amazon.com/step-functions/latest/dg/welcome.html)
- [Azure Logic Apps](https://learn.microsoft.com/en-us/azure/logic-apps/)
- [Google Cloud Workflows](https://cloud.google.com/workflows/docs)

**Internal**

- [`ra-single-agent.md`](../ra/ra-single-agent.md)
- [`ra-multi-agent.md`](../ra/ra-multi-agent.md)
- [Reference Architectures workstream README](../README.md)
- [Working Group Charter](../../../charter/charter.md)
- [Reference Architecture Proposal](../../../Workflows-and-Processes-Reference-Architecture-Proposal.md)

**Source-quality note.** OMG's BPMN/DMN/CMMN specifications, the MCP specification, the A2A specification, the Linux Foundation press releases, NIST, and OWASP are primary or standards sources. Camunda, Temporal, Dapr, LangGraph, CrewAI, Microsoft AutoGen, OpenAI Agents SDK, n8n, AWS Step Functions, Azure Logic Apps, and Google Cloud Workflows entries are vendor documentation, reflecting how each vendor describes its own product rather than independent verification. Anthropic's *Building Effective Agents* and the AAIF blog post are vendor/foundation position pieces, not standards. For the Forrester "Adaptive Process Orchestration" category, Gartner's agentic-project projections, and any other statistic, the prior proposal (`../../../Workflows-and-Processes-Reference-Architecture-Proposal.md`) is the citable source. This survey deliberately does not restate those figures. Every passage across this `docs/` folder marked `> **[verify]**` (including the MCP tool annotation names, A2A's task lifecycle states and `contextId`/`taskId` semantics, Dapr Agents' specific primitive coverage, and the entries for Microsoft Durable Functions, Restate, DBOS, Zapier, Make, Workato, Azure Logic Apps' and Google Cloud Workflows' agentic-feature specifics, CNCF Serverless Workflow's current state, and CrewAI's and AutoGen's specific current API surface) must be confirmed against a primary source before any of this content is used in external-facing WG publication. The same standard applies to the Andrew Ng *The Batch* citation recorded in [`reconciliation-log.md`](reconciliation-log.md).

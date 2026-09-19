# 10 — 🔧 Additional capabilities

**Depth: Awareness only.** Documentation checked: **2026-09-18**. No labs or framework internals.

The examples below are optional extensions to the SRE assistant. Learn the responsibility and boundary before selecting a capability.

| Capability | What / why | How it works | Platform example / interview hook |
|---|---|---|---|
| **Browser** | Managed isolated browsing for web interfaces | Start a browser session and automate page interactions; supported viewing/recording aids investigation | Read an approved status portal without an API. Browser access still needs scoped credentials and egress controls. [AWS](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/browser-tool.html) |
| **Code Interpreter** | Sandboxed code execution for data processing | Execute code against supplied data in a managed session | Aggregate a sanitized latency dataset. Sandbox isolation does not justify broad AWS privileges. [AWS](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/code-interpreter-tool.html) |
| **A2A / multi-agent** | Protocol support for collaborating agents | Runtime can host an A2A server with Agent Card discovery and protocol messaging | An incident coordinator consults a deployment specialist. A2A is a protocol, not a separate replacement for Runtime. [AWS](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/runtime-a2a.html) |
| **Registry** | Governed discovery of agents, MCP servers, tools, skills, and other resources | Publish metadata, curate/approve records, then search the catalog | Find the approved EKS diagnostic tool. Catalog approval is not runtime authorization. The dedicated guide uses **AWS Agent Registry**; the AgentCore overview also uses **AgentCore Registry**. [AWS](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/registry.html) |
| **Harness** | Managed agent orchestration for a configuration-driven starting point | Declare model, instructions, and tools; Harness manages the loop and uses Runtime underneath | Assemble an incident assistant without owning its agent loop. Runtime hosts; Harness orchestrates. [AWS](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/harness.html) |
| **Optimization** | Improve behavior from evaluation evidence | Generate prompt/tool-description recommendations, version configuration bundles, and compare variants with controlled A/B tests | Reduce wrong-tool choices before promotion. This is configuration improvement, not a requirement to train a model. [AWS](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/optimization.html) |

**How they fit:** Registry helps discover approved resources; Harness can orchestrate them; Browser and Code Interpreter add optional execution capabilities; A2A supports collaboration; Optimization uses quality evidence to improve configuration.

**Interview:** Start with the smallest useful diagnostic agent. Add capabilities when a concrete operational need justifies their extra permissions, cost, and failure modes.

> ☁️ AWS's capability catalog evolves. The table is the requested awareness scope, not an exhaustive service inventory. Verify naming, Region availability, release status, and integration support before future projects. [Current AgentCore catalog](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/what-is-bedrock-agentcore.html)

[Next: SRE reference architecture →](11-sre-reference-architecture.md)

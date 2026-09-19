# 12 — ⚡ Interview cheatsheet

**Depth: Compact revision.** Documentation checked: **2026-09-18**. Follow chapter links for explanations and official sources.

## Component recall

| Component | One-line purpose |
|---|---|
| [Bedrock](00-bedrock-foundations.md) | Managed model inference, with retrieval and content-safety capabilities |
| [Runtime](03-agentcore-runtime.md) | Host and operate agent/tool applications |
| [Gateway](04-agentcore-gateway-mcp.md) | Provide a governed connection to tools and other targets |
| [Identity](05-agentcore-identity-iam.md) | Manage agent workload identity and credential access |
| [Policy](06-agentcore-policy-security.md) | Authorize tool calls using explicit rules outside the model |
| [Observability](07-agentcore-observability.md) | Investigate execution through metrics, logs, and traces |
| [Memory](08-agentcore-memory.md) | Preserve selected conversational context |
| [Evaluations](09-agentcore-evaluations.md) | Assess task and tool-use quality |
| [Browser](10-agentcore-additional-services.md) | Run isolated web interaction sessions |
| [Code Interpreter](10-agentcore-additional-services.md) | Execute data-processing code in a sandbox |
| [Registry](10-agentcore-additional-services.md) | Discover curated agents, tools, and related resources |
| [Harness](10-agentcore-additional-services.md) | Supply a managed agent orchestration loop |
| [Optimization](10-agentcore-additional-services.md) | Improve and compare agent configurations using evidence |

This is the repository's component scope, not an exhaustive AWS catalog. A2A is a collaboration protocol; MCP is a tool/context protocol.

## Flows to say out loud

- **Reasoning:** Engineer → authenticated application → agent in Runtime ↔ Bedrock FM.
- **Evidence:** Agent → RAG/runbooks; agent → Gateway → Policy decision → MCP/API/Lambda tool → operational backend.
- **Change:** Evidence → recommendation → human approval → approval validation → narrow executor → health verification.
- **Debugging:** Request → Runtime → model → Gateway → tool → AWS/EKS resource; correlate trace and session IDs.

| Comparison | Remember |
|---|---|
| Bedrock vs AgentCore | Model/data capabilities vs the agent platform; they work together |
| IAM vs Identity | AWS API permissions vs workload identity and credential handling |
| Policy vs Guardrails | Action authorization vs configured content safeguards |
| Memory vs RAG | Conversation-derived context vs retrieved reference knowledge |
| Runtime vs Harness | Hosting/execution vs managed orchestration |

## 15 likely questions and concise answers

| # | Question | Expected answer |
|---|---|---|
| 1 | Why isn't an FM API enough for an SRE agent? | Production also needs execution, tools, identities, permission checks, state, and telemetry. |
| 2 | How would you deploy and release on Runtime? | Package a compatible artifact, configure role/network/auth, deploy a version, validate, then promote a named endpoint. |
| 3 | What is a Runtime session? | An execution/conversation routing boundary; microVM sessions isolate compute. Session IDs are not credentials and required state needs deliberate persistence. |
| 4 | How do you roll back? | Repoint the named endpoint to a tested prior version; verify behavior and handle active sessions deliberately. `DEFAULT` follows updates. |
| 5 | What does Gateway add to MCP? | A managed entry point for compatible targets, connectivity, authentication integration, and configured policy enforcement. |
| 6 | Why separate inbound and outbound authentication? | The caller reaching Gateway and Gateway reaching its target are different trust boundaries with different credentials. |
| 7 | Does AgentCore Identity replace IAM? | No. Identity manages workload/credential flows; AWS API calls still need IAM permissions. |
| 8 | How does Cedar decide? | A matching permit and no matching forbid allow access. No permit denies access; forbid wins. |
| 9 | Can Guardrails prevent an unauthorized restart? | Content filtering is insufficient. Enforce tool policy, backend permissions, and trusted approval checks. |
| 10 | Where would you start with AccessDenied? | Identify the failing hop and actual principal, then check its IAM/resource policy, tool policy, or backend authorization. |
| 11 | The agent is slow. What do you inspect? | Per-step traces, model/token volume, Gateway target duration, retries, throttles, and startup/session behavior. |
| 12 | When do you use Memory versus RAG? | Memory for conversation context; RAG for runbooks. Use live tools for current cluster facts. |
| 13 | How do you know an agent is correct? | Evaluate representative incidents and tool arguments; combine quality checks with operational metrics and human review. |
| 14 | How do you make remediation safe? | Bind external approval to exact parameters and expiry; separate executor permissions, prevent duplicates, and verify health. |
| 15 | What changed about Bedrock Agents? | The older service is now Agents Classic in maintenance mode and closed to new customers from July 30, 2026; this does not retire Bedrock itself. |

Current terminology reference: [AWS maintenance notice](https://docs.aws.amazon.com/bedrock/latest/userguide/agents-classic-maintenance-mode.html). Operational answers summarize the source-linked chapters rather than prescribe deployable configuration.

**Final rehearsal:** Explain the [payment-api reference architecture](11-sre-reference-architecture.md) in two minutes: evidence, identities, permission boundaries, approval, execution, verification.

[Back to learning sequence](../README.md)

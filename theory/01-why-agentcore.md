# 01 — 🤖 Why AgentCore?

**Depth: Working knowledge.** Documentation checked: **2026-09-18**.

## What problem does it solve?

A model can suggest why `payment-api` is unhealthy. A production agent also needs a place to run, authenticated tools, permission checks, state, and operational visibility. Amazon Bedrock AgentCore offers modular capabilities for these responsibilities; applications can use selected services independently. [AgentCore overview](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/what-is-bedrock-agentcore.html)

| Question | Amazon Bedrock | Amazon Bedrock AgentCore |
|---|---|---|
| Primary role in this workspace | Model inference, retrieval, and content safeguards | Build, host, connect, govern, and operate agents |
| Example | Interpret logs using an FM and retrieve a runbook | Run the SRE assistant and mediate its tools |
| Relationship | Supplies model/data capabilities to the application | Can call Bedrock or other model providers |

This is a practical division of responsibilities, not a claim that the products are unrelated. See [Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/what-is-bedrock.html) and [Runtime model flexibility](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/agents-tools-runtime.html).

## Why model access alone is insufficient

| Production need | Component | Example design requirement |
|---|---|---|
| Execution and scaling | Runtime | Host concurrent incident sessions |
| Tool connectivity | Gateway | Expose a narrowly scoped deployment-status tool |
| Workload credentials | Identity | Authenticate to an approved operations API |
| Action authorization | Policy plus IAM/backend controls | Limit which tools and resources the assistant may access |
| Conversation continuity | Memory | Recall which cluster the engineer selected |
| Operational evidence | Observability | Locate a slow model call or denied tool call |
| Behavioral quality | Evaluations | Check whether diagnoses cite the correct evidence |

These roles form a platform design; the individual chapters explain configuration and boundaries. [AWS component catalog](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/what-is-bedrock-agentcore.html)

## How the pieces help

1. Authenticate the engineer and establish their allowed scope.
2. Invoke the agent hosted on Runtime.
3. Let the agent request model reasoning and relevant tool evidence.
4. Enforce permissions before executing tools.
5. Return a diagnosis with evidence and record useful telemetry.

**SRE example:** A model proposes inspecting pod events. The platform allows a read-only event tool for `payments`, but a rollout requires a separate approval-controlled path.

> ⚙️ Runtime hosts an agent; your code or a managed **Harness** supplies its orchestration loop. Hosting alone does not implement a complete agent. [Harness concepts](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/harness.html)

## Interview recall

- An FM generates output; an agent repeatedly combines reasoning, context, and tools to pursue a task.
- Managed hosting reduces infrastructure work. You still own permission design, tool correctness, failure handling, and release quality.
- AgentCore does not require deploying every component at once.

[Next: Architecture →](02-agentcore-architecture.md)

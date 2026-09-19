# 09 — 📊 AgentCore Evaluations

**Depth: Working knowledge.** Documentation checked: **2026-09-18**.

## What and why

A fast, error-free response can still contain a wrong diagnosis. AgentCore Evaluations assesses agent behavior, including task completion and tool-use quality. It can evaluate agents hosted inside or outside Runtime. [How Evaluations works](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/how-it-works-evaluations.html)

Supported telemetry is scored using built-in or custom evaluators. AWS documents LLM-as-a-judge evaluation and supported framework/instrumentation combinations; arbitrary logs are not automatically a compatible evaluation dataset. [Evaluations overview](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/evaluations.html)

## How to think about a quality check

**Representative incident → captured agent/tool trace → evaluator → score and explanation → reviewed improvement.**

Use on-demand evaluation for selected traces and online evaluation for configured production sampling. Recheck supported evaluation modes and telemetry requirements when adding a lab. [Available evaluation modes](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/evaluations.html)

| Dimension | Proposed payment-api check | Failure example |
|---|---|---|
| Response quality | Diagnosis cites evidence and states uncertainty | Confident claim without events or logs |
| Task completion | Explains likely cause and gives a usable next step | Merely repeats “service unhealthy” |
| Tool correctness | Uses the intended cluster, namespace, workload, and time range | Reads a similarly named staging service |
| Safety | Does not execute a write without valid approval | Treats a prompt instruction as authorization |
| Reliability | Handles unavailable evidence explicitly | Invents pod events after tool timeout |

These are a suggested evaluation rubric, not exact AWS evaluator names.

## Example production practice

Before promoting a prompt, model, or tool-schema change, replay a small set of representative scenarios: bad readiness configuration, a healthy service, wrong namespace, denied access, unavailable tools, stale runbooks, and malicious instructions embedded in logs.

- Compare the new behavior with the previous release on the same cases.
- Add deterministic assertions for exact tool arguments and absence of unauthorized writes.
- Review evaluator disagreements with an engineer; judge-model scores are not ground truth.
- Monitor sampled production quality alongside latency, errors, and token usage.
- Keep evaluation inputs sanitized and record the configuration/version being assessed.

> ⚠️ Evaluation measures behavior. IAM, Policy, and backend controls enforce access. A high safety score is not an execution permission.

**Interview:** “Observability shows what happened and where time went. Evaluation asks whether the agent completed the right task correctly. I use both in release decisions.”

[Next: Additional capabilities →](10-agentcore-additional-services.md)

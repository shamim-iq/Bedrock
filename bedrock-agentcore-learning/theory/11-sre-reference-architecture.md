# 11 — 🤖 AI SRE / Platform Operations Assistant

**Depth: Strong applied synthesis.** Documentation checked: **2026-09-18**.

## What and why

An illustrative assistant answers **“Why is payment-api unhealthy?”**, gathers read-only evidence, recommends a change, and routes any mutation through human approval. This is a proposed architecture, not an AWS-provided turnkey solution or an implemented project.

## End-to-end architecture

```mermaid
flowchart TB
  E["👤 Engineer: Why is payment-api unhealthy?"]
  subgraph ENTRY["Authenticated entry"]
    APP["Operations application"]
    AUTH["🔐 IdP / inbound authorization"]
    APP --> AUTH
  end
  E --> APP
  subgraph AGENT["AgentCore Runtime: isolated incident session"]
    A["🤖 SRE agent orchestration"]
    D["Diagnosis + evidence + proposed remediation"]
    A --> D
  end
  AUTH --> A
  subgraph REASON["Model and reference knowledge"]
    FM["☁️ Bedrock FM"]
    GR["🔐 Configured Bedrock Guardrails"]
    KB["📚 Knowledge Bases: approved runbooks"]
    MEM["🧠 Scoped AgentCore Memory"]
    GR -.-> FM
  end
  A <--> FM
  A <--> KB
  A <--> MEM
  subgraph READ["Governed diagnostic path"]
    G["🔧 AgentCore Gateway"]
    P["Policy engine: enforce tool rules"]
    T["MCP tools / API or Lambda adapters"]
    G -->|authorize| P
    G -->|after allow| T
  end
  A <--> G
  subgraph BACKEND["Operational evidence"]
    CW["CloudWatch alarms / logs"]
    K["EKS events / workload status"]
    AR["ArgoCD application / revision"]
  end
  T <--> CW
  T <--> K
  T <--> AR
  subgraph CHANGE["Human-controlled change path"]
    H{"👤 Approve exact change?"}
    V["Validate approver, scope, expiry, operation ID"]
    X["Controlled executor: narrow action role"]
    END["No change; retain diagnosis"]
    H -->|No| END
    H -->|Yes| V
    V -->|valid approval and current state| X
  end
  D --> E
  D --> H
  X -->|approved GitOps change| AR
  AR -->|reconcile desired state| K
  X --> VERIFY["Recheck health + record outcome"]
  VERIFY --> E
  ID["🔐 AgentCore Identity: scoped credential access"] -.-> A
  ID -.-> G
  IAM["IAM + Kubernetes RBAC + ArgoCD authorization"] -.-> T
  IAM -.-> X
  O["📊 OpenTelemetry + CloudWatch + audit records"]
  A -.-> O
  G -.-> O
  T -.-> O
  V -.-> O
  X -.-> O
  classDef agent fill:#e3f2fd,stroke:#1565c0,color:#102a43;
  classDef security fill:#e8f5e9,stroke:#287d3c,color:#172b1a;
  classDef data fill:#fff3e0,stroke:#b66a00,color:#402600;
  classDef human fill:#f3e5f5,stroke:#7b1fa2,color:#301040;
  class A,D,G,T,X agent;
  class AUTH,P,GR,ID,IAM,V security;
  class FM,KB,MEM,CW,K,AR,O data;
  class E,H human;
```

Solid arrows are calls, results, or workflow progression; dotted arrows are controls/telemetry. The application orchestrates retrieval and model calls. The separate change path is a design choice: this diagnostic agent cannot directly invoke the executor or change backend state.

## One incident, step by step

| Step | Evidence / decision | Required boundary |
|---|---|---|
| 1. Establish scope | Engineer selects production, cluster, `payments`, `payment-api` | Validate user access; do not trust prompt-supplied tenant identity |
| 2. Gather context | Retrieve the current readiness runbook and selected conversation history | Authorized retrieval and memory scope |
| 3. Read live state | Query alarms, pod events, and deployed revision through Gateway tools | Policy, outbound credentials, IAM/RBAC |
| 4. Diagnose | Correlate readiness failures with a recent configuration change | Cite timestamps and sources; disclose unavailable evidence |
| 5. Recommend | Propose an exact reviewed configuration revert with expected impact | No mutation yet; attach the diff and rollback considerations |
| 6. Approve | Authorized engineer reviews action, environment, and risk | External trusted approval record, expiry, unique operation ID |
| 7. Execute | Executor validates approval and current revision, then uses the allowed GitOps path | Narrow executor role; prevent duplicate execution |
| 8. Verify | Check readiness/error rate after reconciliation and record outcome | Read-only checks; escalate if recovery is not demonstrated |

**Illustrative diagnosis:** “Readiness failures began after revision R42 changed the probe path. Current events and the approved runbook support reverting that configuration. I have not changed production.” This is an example, not a finding from a real cluster.

## Production design choices to remember

- Private EKS/ArgoCD connectivity needs explicitly designed network paths; Gateway does not make private endpoints reachable automatically.
- No general-purpose privileged shell tool. Prefer typed, bounded operations and restricted output.
- Treat runbooks, logs, and tool responses as data that may contain prompt injection.
- Keep approval/action status in an authoritative workflow store. A Memory record cannot approve a rollout.
- Record who requested, approved, and executed the change alongside endpoint version, tool arguments, operation ID, and outcome.
- If evidence is missing, return a partial diagnosis. If a write times out, reconcile its status before retrying.
- Evaluate the assistant against known incidents before promoting configuration changes.

These are recommended design decisions assembled from the earlier chapters. Service building blocks are documented in [Runtime](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/agents-tools-runtime.html), [Gateway](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/gateway-core-concepts.html), [Policy](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/policy-core-concepts.html), and [observability setup](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/observability-configure.html).

**Interview:** “My assistant diagnoses with scoped read-only tools. A trusted workflow approves a concrete change, a separate executor applies it, and telemetry plus health checks prove the outcome.”

[Next: Interview cheatsheet →](12-interview-cheatsheet.md)

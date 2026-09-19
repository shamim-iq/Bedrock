# 05 — 🔐 AgentCore Identity + IAM

**Depth: Strong.** Documentation checked: **2026-09-18**.

## What and why

AgentCore Identity manages workload identities and access to credentials for agent applications. It integrates with identity providers and supports agent access to services on its own behalf or on behalf of users. A workload identity identifies the agent; it is not interchangeable with an IAM execution role. [Identity](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/identity.html)

**Authentication** establishes who is calling. **Authorization** decides what that caller may do. A valid token is insufficient evidence that its owner may restart production.

## How identity crosses the architecture

| Boundary | Identity / mechanism | Authorization question |
|---|---|---|
| Engineer → application / Runtime | Trusted IdP token or IAM-signed invocation, as configured | May this caller use this agent and incident scope? |
| Runtime application → AWS services | Runtime execution role and temporary AWS credentials | May this workload invoke the selected model or access the selected memory? |
| Agent → Gateway | Configured IAM or OAuth identity | Which caller does Gateway and its Policy engine actually see? |
| Gateway → Lambda / API / MCP server | Gateway role or supported outbound credentials | May Gateway invoke this specific target? |
| Tool → AWS / Kubernetes / ArgoCD | Tool-specific role, service account, or API credential | Which backend resources and operations are allowed? |

AgentCore Identity supports credential flows involving SigV4, OAuth, and API keys. OAuth user delegation and machine-to-machine access have different consent and scope requirements. Do not assume an incoming user's token is automatically propagated to every downstream service. [Identity overview](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/identity-overview.html), [Gateway credential boundaries](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/gateway-core-concepts.html)

## Familiar AWS analogy

With EKS workload identity, a pod receives credentials for a role rather than embedding an administrator's access key. Apply the same separation of workload and person here:

- **Trust policy:** who or what may assume a role.
- **Permissions policy:** what that assumed role may access.
- **Temporary credentials:** time-limited AWS credentials that applications refresh through supported mechanisms; never place them in a prompt or repository. [IAM temporary credentials](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html)
- **AgentCore workload identity:** an identity used in agent-specific credential access flows; it does not replace AWS IAM permission evaluation.

## Least-privilege design for payment-api

Proposed role separation:

1. **Runtime role:** model inference, selected memory operations, telemetry, and allowed tool connectivity. No direct cluster mutation.
2. **Gateway role:** invoke approved tool targets; no broad account-administration permissions.
3. **Diagnostic tool role:** read the required operational evidence only.
4. **Remediation executor role:** access the exact permitted change path; reachable only after trusted approval validation.

For EKS, AWS permissions such as describing a cluster do not automatically grant Kubernetes object access. Configure the appropriate EKS access entries/access policies or Kubernetes RBAC for the tool identity. Scope access to the required namespaces and resources. [EKS access permissions](https://docs.aws.amazon.com/eks/latest/userguide/access-policy-permissions.html)

## Resource policies

IAM identity policies attach to callers; resource policies attach to resources. AgentCore documents resource policies for Runtime/endpoints, Gateway, and Memory; Evaluations also documents policies on its own resource types. Verify support for the exact API/resource instead of assuming universal support. Cross-account Runtime invocation needs attention to **both Runtime and endpoint** policies. [AgentCore resource policies](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/resource-based-policies.html), [Evaluations access](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/evaluations.html)

> ⚠️ Inspect explicit denies, organization controls, permission boundaries, and resource conditions when diagnosing access failures. Do not “fix” a denial by adding administrator access.

## Example and interview recall

The engineer can ask about `payment-api`, but the assistant uses a diagnostic identity to read events. If Gateway sees a shared machine identity, it cannot infer the engineer's team from the conversation. Enforce user scope through trusted identity propagation or an authorized application layer, and validate it again in the tool.

**Interview answer:** “I trace the principal at each hop. Inbound authentication protects entry, outbound authentication protects target access, IAM grants AWS permissions, and Policy constrains tool use. Kubernetes and ArgoCD retain their own authorization boundaries.”

[Next: Policy + security →](06-agentcore-policy-security.md)

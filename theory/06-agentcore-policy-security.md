# 06 — 🔐 Policy + security

**Depth: Working knowledge.** Documentation checked: **2026-09-18**.

## What, why, and how

AgentCore Policy evaluates tool authorization outside the model. A **policy engine** holds policies and is associated with a Gateway. Cedar rules describe a **principal**, **action**, **resource**, and optional conditions. In enforcing mode, a call requires a matching `permit` and no matching `forbid`: no permit means DENY, and forbid wins. [Policy concepts](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/policy-core-concepts.html)

| Control | Main responsibility | payment-api example | Boundary |
|---|---|---|---|
| Bedrock Guardrails | Evaluate configured content safeguards | Filter sensitive information in output | Does not authorize a rollout |
| AgentCore Policy | Decide which tool calls are allowed | Restrict event queries to an approved namespace | Does not grant the target's AWS permissions |
| AgentCore Identity | Workload identity and credential access | Obtain credentials for an operations API | Identity is not blanket permission |
| AWS IAM | Authorize AWS API access | Allow a tool role to read specific operational data | Does not judge diagnostic correctness |

Sources: [Guardrails](https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails-how.html), [Identity](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/identity.html), [IAM credentials](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html). These controls complement each other; Kubernetes RBAC and backend validation still apply.

## Read and modify a simple Cedar policy

**Illustrative only, not deployment-ready.** This assumes OAuth authentication, a trusted `role` claim, and a tool schema with required string `namespace`. Replace the example account, Gateway ARN, and action with values from the actual generated schema. It grants read-only event access to the `payments` namespace.

```cedar
permit (
    principal is AgentCore::OAuthUser,
    action == AgentCore::Action::"OpsTools___get_workload_events",
    resource == AgentCore::Gateway::"arn:aws:bedrock-agentcore:us-east-1:123456789012:gateway/EXAMPLE"
)
when {
    principal.hasTag("role") &&
    principal.getTag("role") == "platform-reader" &&
    context.input.namespace == "payments"
};
```

- `principal`: restricts the caller type; the trusted IdP supplies claims.
- `action`: the exact Gateway tool action, including its target prefix.
- `resource`: the Gateway being accessed, not the EKS cluster ARN.
- `when`: narrows permission using caller attributes and tool arguments.
- To adapt it, change the permitted namespace or approved role, then revalidate against the tool schema. Missing or mismatched fields must not be treated as approval.

The entity layout and `context.input`/claim access follow [policy scope](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/policy-scope.html) and [policy conditions](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/policy-conditions.html).

| Request, assuming this is the only permit | Expected decision |
|---|---|
| Platform reader, event tool, `payments` | ALLOW |
| Same caller, another namespace | DENY |
| Missing role claim | DENY |
| Restart tool instead of event tool | DENY |
| Matching permit plus matching forbid | DENY |

Use `ENFORCE` for actual blocking. `LOG_ONLY` is useful for observing prospective effects but must not be mistaken for protection. [Gateway association](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/create-gateway-with-policy.html), [policy testing mode](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/policy-test-a-policy.html)

## Human approval for dangerous operations

Proposed workflow: **diagnosis → exact change proposal → authenticated approver → verified approval record → controlled executor → health verification**.

- Bind approval to the service, environment, action, parameters, expiry, and unique operation ID.
- Keep approval issuance outside the model's control. A tool argument like `approved: true` is untrusted input.
- Recheck approval and current resource state immediately before execution. Prevent replay and duplicate writes.
- Restrict the agent's direct backend permissions so it cannot bypass the controlled executor.

AWS also documents Cedar-compatible **Dogwood temporal policies** for session-history conditions, including prior-event requirements. This is awareness only here; such policy history does not by itself authenticate a human approver. [Temporal policies](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/policy-temporal.html)

**Interview:** Prompt injection can arrive through logs or runbooks. Treat retrieved text as data, and enforce tool permissions independently of what the model says.

[Next: Observability →](07-agentcore-observability.md)

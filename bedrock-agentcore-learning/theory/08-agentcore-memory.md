# 08 — 🧠 AgentCore Memory

**Depth: Working knowledge.** Documentation checked: **2026-09-18**.

## What and why

AgentCore Memory stores conversational information so an agent can maintain useful context. A model invocation does not automatically remember earlier requests; the application retrieves relevant context and supplies it again. Memory supports short-term interactions and longer-lived information across sessions. [Memory overview](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/memory.html)

| Type | How it helps | payment-api example |
|---|---|---|
| Short-term | Preserve turn-by-turn events for a session | “That deployment” refers to the revision discussed in the previous turn |
| Long-term | Retain selected facts, preferences, or summaries across sessions | Remember that the team prefers UTC incident timelines |

Long-term extraction depends on configured memory strategies; retaining an event does not mean every fact immediately becomes a durable, verified memory. [Memory architecture and strategies](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/how-it-works.html)

## How the application uses it

1. Associate events with the correct actor and conversation/session.
2. Store relevant interactions under the configured memory resource.
3. Retrieve recent events and appropriate long-term records.
4. Add only useful retrieved context to the next model request.

The application must enforce who can read or write each actor/session namespace. An identifier supplied by a client is not authorization.

## Keep these kinds of state separate

| State | Purpose | Example |
|---|---|---|
| Runtime session | Execution environment continuity | Active incident investigation process |
| AgentCore Memory | Conversation and selected cross-session context | Previously selected cluster |
| Knowledge Bases / RAG | Reference material | Approved readiness-probe runbook |
| Live tool result | Current operational evidence | Pod events fetched at 10:05 UTC |
| Workflow/audit store | Authoritative action status | Approved operation ID and execution outcome |

This separation is a suggested platform design. Never use conversational memory as the authoritative approval record or assume an old deployment status still describes production.

## SRE use and interview recall

Useful memory reduces repeated questions during an incident and supports shift handovers. Before reusing it, check freshness, source, tenant scope, retention, and deletion needs. Avoid storing credentials or unfiltered logs, and treat remembered content as potentially untrusted.

**Interview:** “Short-term memory supports a conversation; long-term memory carries selected information across conversations. RAG retrieves reference knowledge. Live tools validate current state. None of these replace authorization.”

[Next: Evaluations →](09-agentcore-evaluations.md)

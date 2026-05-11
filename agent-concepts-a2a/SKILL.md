---
name: agent-concepts-a2a
description: Explains Agent-to-Agent Protocol (A2A) — the open standard by Google, now Linux Foundation, for inter-agent communication. Covers Agent Cards (JSON discovery documents), Task lifecycle, JSON-RPC/gRPC/SSE/HTTP+REST transports, v1.0 stable (March 2026), and 150+ supporting organizations. Use when discussing multi-agent systems, agent coordination, A2A protocol, agent discovery, or when user mentions agents collaborating, delegating tasks, or working across frameworks.
---

# Agent-to-Agent Protocol (A2A)

A2A is the open standard for inter-agent communication. Announced by Google (April 2025) with 50+ launch partners, now Linux Foundation-governed with 150+ organizations. v1.0 stable released March 2026.

**Purpose**: Enable agents built on different frameworks, by different vendors, on different infrastructure, to discover each other, delegate tasks, and exchange results — without sharing internal implementation details.

## Core abstractions

### Agent Card
A JSON document published at a well-known URL (`/.well-known/agent-card.json`) that describes an agent's:
- Identity (name, description, version, provider)
- Capabilities (streaming, push notifications, multi-turn)
- Skills (named units of capability with input/output modes)
- Endpoints (URL + transport + protocol version)
- Security (authentication schemes, requirements)

Agent Cards enable **dynamic discovery** — one agent can find and understand another at runtime without hardcoded integrations.

```json
{
  "protocolVersion": "1.0",
  "name": "Recipe Agent",
  "description": "Helps with recipes and cooking.",
  "url": "https://recipes.example.com/a2a",
  "version": "1.0.0",
  "capabilities": { "streaming": true, "pushNotifications": true },
  "skills": [{ "name": "find_recipe", "inputModes": ["text"], "outputModes": ["text", "file"] }],
  "defaultInputModes": ["text"],
  "defaultOutputModes": ["text", "file"]
}
```

### Task
The fundamental unit of work in A2A. Tasks have a lifecycle:
```
submitted → working → input-required / completed / failed / canceled / rejected
```
Tasks produce **Artifacts** — outputs like files, structured data, or messages. Long-running tasks support status polling and cancellation.

### Message
Messages carry context between agents: text, files, structured data (JSON), or UI components. Each message part declares its MIME type, enabling **modality negotiation** between agents.

## Protocol bindings

A2A defines operations at an abstract layer, then maps them to concrete transports:

| Binding | Transport | Use case |
|---------|-----------|----------|
| **JSON-RPC 2.0** | HTTP + SSE | Primary binding. Synchronous + streaming |
| **gRPC** | HTTP/2 | High-performance, strongly typed (added v0.3) |
| **HTTP+REST** | Standard HTTP | For teams wanting REST-native toolchain |

## Key operations (binding-independent)

| Operation | Purpose |
|-----------|---------|
| Send Message | Request an agent to do work (sync) |
| Stream Message | Request work with real-time progress (SSE) |
| Get Task | Poll task status |
| List Tasks | Query tasks by filter |
| Cancel Task | Abort a running task |
| Subscribe | Get push notifications for task events |

## Key design principles

- **Opaque execution**: Agents collaborate based on declared capabilities only — they don't share internal reasoning, plans, or tool implementations
- **Async-first**: Designed for long-running tasks (potentially hours/days) and human-in-the-loop interactions
- **Enterprise-ready**: Built-in support for OAuth 2.0, mTLS, API keys, signed Agent Cards, multi-tenancy, and observability

## Governance and ecosystem

- **Linux Foundation**: Donated June 2025, alongside MCP under AAIF
- **150+ partners**: AWS, Microsoft, Google, Salesforce, SAP, ServiceNow, Cisco, IBM, and more
- **5 SDKs**: Python, JavaScript/TypeScript, Java, Go, .NET
- **Platform integrations**: Azure AI Foundry, Copilot Studio, Amazon Bedrock AgentCore Runtime

## A2A vs. MCP

A2A is agent↔agent (horizontal). MCP is agent↔tool (vertical). They compose:
- An A2A endpoint can use MCP internally for its own tool surface
- An MCP server cannot substitute for A2A — it lacks agent semantics (no identity, no task lifecycle)

## References

- Specification: https://a2a-protocol.org
- GitHub: https://github.com/google/A2A

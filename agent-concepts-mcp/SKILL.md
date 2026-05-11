---
name: agent-concepts-mcp
description: Explains Model Context Protocol (MCP) — the agent-to-tool standard by Anthropic, now Linux Foundation. Covers client-server architecture, JSON-RPC transport, Streamable HTTP (v2), tools/resources/prompts primitives, OAuth 2.1 auth, capability negotiation, and 18,000+ community servers. Use when discussing MCP, agent tool integration, MCP servers/clients, or when user asks about connecting agents to external tools, APIs, databases, or file systems.
---

# Model Context Protocol (MCP)

MCP is the de facto standard for connecting AI agents to external tools, data sources, and services. Launched by Anthropic (Nov 2024), now governed by the Linux Foundation's Agentic AI Foundation (AAIF). Analogous to "USB-C for AI agents" — one standard interface for any tool.

**97M+ SDK downloads, 18,000+ community MCP servers, supported by all major AI platforms.**

## Architecture

```
Agent (Host) → MCP Client → MCP Server → Tool/Resource
```

- **Host**: The AI application (Claude, IDE, custom agent) that initiates connections
- **Client**: Connector within the host maintaining 1:1 connection with a server
- **Server**: Service exposing tools, resources, and prompts to the agent

MCP is agent↔tool (vertical), NOT agent↔agent (horizontal). For agent-to-agent communication, see A2A.

## Primitives (what servers can expose)

| Primitive | Purpose | Example |
|-----------|---------|---------|
| **Tools** | Functions the agent can call | `search_database(query)`, `create_issue(title, body)` |
| **Resources** | Context/data the agent can read | File contents, API responses, database records |
| **Prompts** | Templated interaction patterns | "Summarize this document" with pre-loaded context |
| **Sampling** | Server-initiated LLM requests | Server asks the agent to generate something |

## Transport

**Streamable HTTP** (2025-03-26 specification, also called "v2"):
- Single `/mcp` endpoint for both requests and streaming
- Server responds immediately for fast ops, upgrades to SSE for long-running tasks
- Stateless — works behind standard load balancers without sticky sessions
- Replaced the old dual-endpoint model (HTTP POST + SSE GET)

**STDIO** transport still supported for local/embedded use cases.

## Protocol details

- **Message format**: JSON-RPC 2.0
- **Lifecycle**: Initialize → capability negotiation handshake → operate → terminate
- **Capability negotiation**: Client and server declare supported features during initialization
- **Versioning**: Protocol version identified by date string (e.g., "2025-11-25")

## Authentication

HTTP-based transports use OAuth 2.1 with PKCE and Resource Indicators. STDIO transports retrieve credentials from environment variables.

## Key specification milestones

| Date | Version | What changed |
|------|---------|-------------|
| Nov 2024 | Initial | Core spec: JSON-RPC, tools, resources, prompts |
| 2025-03-26 | Streamable HTTP | Single endpoint, stateless, load-balancer-friendly |
| 2025-06-18 | icons, elicitation | Server icons, user elicitation requests |
| 2025-11-25 | Auth framework | OAuth 2.1, `_meta`, formal auth spec |

## Discovery and server ecosystem

- Community registries: glama.ai, mcp.so
- 18,000+ indexed servers across all categories
- Official reference implementations and SDKs in Python, TypeScript, Java, Kotlin, Go, C#

## MCP vs. A2A

MCP handles **vertical** connections (agent ↔ tool). A2A handles **horizontal** connections (agent ↔ agent). They are complementary:
- An A2A agent can internally use MCP to access its tools
- An MCP-connected agent may never need A2A if it works alone
- The two-layer stack (MCP + A2A) is the reference architecture for enterprise agent systems

## References

- Specification: https://modelcontextprotocol.io
- GitHub: https://github.com/modelcontextprotocol/modelcontextprotocol

---
name: agent-concepts-stack
description: Explains the AI agent protocol ecosystem and layer architecture. Covers the three-layer model (tool layer MCP/WebMCP, coordination layer A2A/ACP/ANP, commerce/UI layer UCP/AP2/A2UI/AG-UI), Linux Foundation AAIF governance, and how protocols compose rather than compete. Use when discussing agent architecture design, protocol selection, multi-agent system planning, or when user asks how agent standards fit together or which protocol to choose.
---

# AI Agent Protocol Ecosystem

As of 2026, the agent interoperability landscape has crystallized into a layered architecture. Understanding the layers is essential for architecture decisions — no single protocol does everything.

## The three-layer model

```
┌─────────────────────────────────────────────────────┐
│ Layer 3: Commerce & UI                              │
│ UCP / AP2 / x402 (payments)  |  A2UI / AG-UI (UI)  │
├─────────────────────────────────────────────────────┤
│ Layer 2: Agent Coordination (Horizontal)            │
│ A2A  |  ACP  |  ANP                                 │
├─────────────────────────────────────────────────────┤
│ Layer 1: Tool Integration (Vertical)                │
│ MCP  |  WebMCP                                      │
└─────────────────────────────────────────────────────┘
```

### Layer 1 — Tool Integration (agent↔tool)

| Protocol | Role | Status |
|----------|------|--------|
| **MCP** | Universal agent↔tool standard | **Dominant.** 97M downloads, 18K+ servers |
| **WebMCP** | Browser-native variant; websites expose tools to in-browser agents | W3C Community Group, preview in Chrome Canary (Feb 2026) |

This layer is **settled**. MCP has won. New tool integrations should use MCP.

### Layer 2 — Agent Coordination (agent↔agent)

| Protocol | Origin | Architecture | Status |
|----------|--------|-------------|--------|
| **A2A** | Google (Apr 2025) | Peer-like | **Leading.** v1.0 stable, 150+ orgs, embedded in Azure/AWS |
| **ACP** | IBM/AGNTCY (2024) | Brokered (registry) | Niche. REST-native, multipart MIME, Linux Foundation |
| **ANP** | Community | P2P decentralized | Experimental. W3C DIDs, JSON-LD, trustless marketplace |

A2A is the default choice. ACP is an alternative for teams that want REST-native with broker-based registry. ANP targets decentralized/trustless scenarios.

### Layer 3 — Commerce & UI

| Protocol | Purpose | Notes |
|----------|---------|-------|
| **UCP** (Universal Commerce Protocol) | Standardize shopping lifecycle | Google, strongly typed schemas |
| **AP2** (Agent Payments Protocol) | Payment authorization with audit trail | Extension of UCP, cryptographic mandates |
| **x402** | Alternative payment protocol | HTTP 402-based |
| **A2UI** | Agent composes dynamic UI from component catalog | Google, declarative JSON, 18 primitives |
| **AG-UI** | How to stream agent output to frontend | Community protocol |

These protocols are for agents that autonomously transact or generate user interfaces. Most agent systems don't need them yet.

## Additional standards (cross-cutting)

| Standard | Purpose | Origin | Date |
|----------|---------|--------|------|
| **Agent Skills** | Package agent capabilities and workflows | Anthropic | 2025 |
| **AGENTS.md** | Project-level instructions for coding agents | OpenAI | Aug 2025 |
| **Open Responses** | Standardized agent loop protocol | OpenAI | 2026 |

## Governance: Linux Foundation AAIF

The **Agentic AI Foundation** (formed Dec 2025 under Linux Foundation) is the single most important structural fact of 2026. MCP, A2A, and ACP all sit under AAIF governance with overlapping membership:

**Board members**: Anthropic, OpenAI, Google, Microsoft, AWS, Block (Square), Cloudflare, Bloomberg

This means:
- **No winner-take-all protocol war**: Competing vendors share the same governance table
- **Complementary layering is the official direction**: Joint MCP/A2A interoperability spec expected Q3 2026
- **Pre-competitive infrastructure**: Companies compete on applications, not on protocols

## Decision guide: which protocol when?

| Scenario | Protocol |
|----------|----------|
| Agent needs to query a database, read files, call an API | **MCP** |
| Two or more agents need to collaborate on a task | **A2A** |
| Agent operates in a browser and needs website tools | **WebMCP** (future) |
| Agent autonomously purchases goods/services | **UCP + AP2** |
| Agent generates UI for user interaction | **A2UI or AG-UI** |
| You want coding agents to follow project conventions | **AGENTS.md** |
| You want to package reusable agent capabilities | **Agent Skills** |

## Key architectural patterns

1. **Start with MCP** — tool access is the foundation. Non-negotiable for production.
2. **Add A2A when coordinating multiple agents** — not before. Single agents don't need A2A.
3. **Each agent internally uses MCP for its tools, A2A externally for coordination**
4. **Build Agent Card endpoints for discoverability** — even if you don't use A2A yet

## References

- AAIF: https://www.linuxfoundation.org/press/agentic-ai-foundation
- NIST AI Agent Standards Initiative: nist.gov (Feb 2026)
- agentskills.io, a2a-protocol.org, modelcontextprotocol.io

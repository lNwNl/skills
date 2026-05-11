---
name: research-beyond-knowledge
description: Guides AI agents to discover concepts and technologies beyond their training data cutoff by breaking out of search bias. Use when researching any domain for new/emerging concepts, conducting technology landscape surveys, exploring post-cutoff developments, or whenever the agent needs to find things it doesn't already know exist. Trigger keywords: research, survey, landscape, ecosystem, what's new, emerging, latest, discover, find out about.
---

# Research Beyond Knowledge Boundaries

AI agents have a structural blind spot: search queries are biased by pre-existing knowledge. You search for what you know, finding more of the same, while entirely new categories remain invisible. This skill provides methodology to break that cycle.

## When to apply

Use this skill when researching **any** domain, especially:
- Technology landscape surveys or ecosystem overviews
- Questions about "what's new" or "emerging" in a field
- Exploring domains where training cutoff matters
- Finding alternatives the agent doesn't know exist

## The problem

A concrete example from the agent protocols domain:
- **First round** (biased): "MCP A2A agent protocols standards" → returned MCP, A2A, AGENTS.md. All things the agent already partially knew.
- **Second round** (correct): "comprehensive list of all AI agent standards specifications protocols open standards 2025 2026" → discovered WebMCP, A2UI, AG-UI, UCP, AP2, x402, ACP, ANP, ERC-8004, Open Responses, AAIF, NIST initiative, and more — most of which the agent had zero prior knowledge of.

## Methodology

### Step 1: Start with category-level queries, not specific terms

Use broad category descriptors, not names of specific implementations:

| Biased (bad) | Unbiased (good) |
|---|---|
| "MCP A2A agent protocols" | "list of all AI agent protocols specifications standards" |
| "React hooks useState useEffect" | "React state management patterns and approaches" |
| "Kubernetes Docker container tools" | "container orchestration platforms and alternatives" |

### Step 2: Use landscape/survey language

These terms signal to search engines to return comprehensive overviews:
- `landscape`, `ecosystem`, `comprehensive list`, `overview`, `survey`, `comparison`
- `all X standards`, `complete guide to X`, `state of X 2026`
- `X vs Y vs Z` (forces comparison across categories)

### Step 3: Time-bound to force recency

Dating queries surfaces post-cutoff content:
- `2025 OR 2026` appended to queries
- `announced`, `released`, `launched`, `introduced`
- `what's new in X 2025 2026`

### Step 4: Exclude known terms to find alternatives

Use negation to force discovery of unknown competitors:
- `"agent" protocol -MCP -A2A` → surfaces ACP, ANP, WebMCP
- After finding one alternative, exclude it too: `-MCP -A2A -ACP` → surfaces ANP

### Step 5: Search meta-sources

Target sources that aggregate across categories:
- Foundation/governance announcements (Linux Foundation, W3C, NIST)
- Analyst reports and roundups ("agent interoperability protocols 2026")
- Conference talks and ecosystem maps
- GitHub topic aggregators and awesome lists

### Step 6: Iterate

Each round of broad search reveals new terms. Feed those into the next round:
1. Broad category search → discover WebMCP, ACP, ANP
2. Target each discovery → learn details, find related terms
3. Those details reveal governance (AAIF), identity (ERC-8004), commerce (UCP)
4. Follow governance to discover NIST, W3C, ITU-T efforts

## Anti-patterns

- **Searching for what you already know**: Confirms bias, misses the unknown
- **Using specific product names in first query**: Narrows results to known competitors only
- **Stopping after one round**: The unknown requires multiple passes
- **Assuming search completeness**: No single query covers everything

## When NOT to use

Skip this methodology when:
- The user asks for specific, known technology (e.g., "how does React useEffect work")
- The task is implementation, not discovery
- The domain is narrow and well-understood by the agent

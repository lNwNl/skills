---
name: agent-concepts-agents-md
description: Explains AGENTS.md — the open standard by OpenAI for project-level AI agent instructions. Covers format (plain Markdown with semantic headings), directory merge rules (cascading from root to current directory, with AGENTS.override.md overrides), cross-platform support (Codex, Copilot, Cursor, Gemini, Jules, Aider), and typical sections. Use when discussing AGENTS.md, project instructions for coding agents, or how to configure AI coding tools for a codebase.
---

# AGENTS.md

AGENTS.md is a simple, open format for guiding AI coding agents. Think of it as a README for agents — a dedicated, predictable place to provide context and instructions that all agentic coding tools can consume.

Launched by OpenAI (Aug 2025). 20,000+ GitHub repos have adopted it. Supported by Codex, Copilot, Cursor, Gemini CLI, Jules, Aider, Zed, Phoenix, Factory Droid, RooCode, and others.

## Format

AGENTS.md is **plain Markdown** — no YAML frontmatter, no JSON schema, no required fields. Convention over configuration. Semantic headings serve as cues:

```markdown
## Build & Test
- Install: `pnpm install`
- Unit tests: `pnpm test:unit`
- Type-check: `pnpm tsc --noEmit`

## Code Style
- ESLint + Prettier enforced; run `pnpm lint:fix` before commit
- Prefer arrow functions; avoid `var`
- File names: kebab-case; React components: PascalCase

## Security Notes
- Never log raw user input
- All DB queries go through the `db` wrapper
```

## Discovery and merge rules

Agents walk the directory tree from root to current working directory, merging files:

```
~/.codex/AGENTS.md              # Global (user-level, loaded first)
project-root/AGENTS.md           # Repository-level
packages/ui/AGENTS.md            # Package-level (loaded last, overrides)
```

### Override mechanism
- `AGENTS.override.md` takes precedence over `AGENTS.md` at the same directory level
- Removed `AGENTS.override.md` → falls back to `AGENTS.md`

### Fallback filenames
Platforms can configure alternative filenames (e.g., `TEAM_GUIDE.md`) via `project_doc_fallback_filenames`.

### Size limits
Default combined limit: ~32KB. When exceeding, files closer to the root are truncated.

## Platform-specific equivalents

Before AGENTS.md, each platform had its own format. AGENTS.md unifies them:

| Platform | Legacy file |
|----------|-------------|
| Claude | `CLAUDE.md` |
| Cursor | `.cursor/rules` |
| GitHub Copilot | `.github/copilot-instructions.md` |
| Gemini | `GEMINI.md` |

AGENTS.md is the cross-platform standard. Existing platform-specific files still work, but AGENTS.md provides a single source of truth.

## Typical sections

| Section | Purpose |
|---------|---------|
| **Build & Test** | Exact commands to build, test, lint, type-check |
| **Code Style** | Conventions, formatters, naming rules |
| **Security Notes** | What NOT to do, sensitive patterns |
| **Project overview** | Architecture, key dependencies, repo layout |
| **PR instructions** | Title format, required checks before merge |
| **Dev environment** | Setup steps, tooling, workspace configuration |

## AGENTS.md vs. other standards

| Standard | Scope | Trigger |
|----------|-------|---------|
| **AGENTS.md** | Project instructions | Loaded unconditionally at session start |
| **Agent Skills** | Task-specific capabilities | Loaded on demand when matched |
| **MCP** | Runtime tool access | Server connection at runtime |
| **README.md** | Human documentation | Not designed for agent consumption |

AGENTS.md complements, not replaces, README.md — it contains the mechanical instructions agents need (commands, conventions, constraints), not the narrative explanation humans need.

## References

- Specification: https://github.com/openai/agents.md/
- Website: https://agents.md

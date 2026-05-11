---
name: agent-concepts-skills
description: Explains Agent Skills open standard for packaging domain expertise, workflows, and resources for AI agents. Covers progressive disclosure (metadata→instructions→resources), SKILL.md format with YAML frontmatter, directory conventions (.agents/skills/), and how skills differ from prompts. Use when discussing or creating agent skills, when asked about skill architecture, modular agent capabilities, or the agentskills.io specification.
---

# Agent Skills

Agent Skills is an open standard (agentskills.io) for extending AI agents with specialized knowledge and workflows. Originally developed by Anthropic, released as an open standard under community governance.

## Core concept

A Skill is a directory containing at minimum a `SKILL.md` file with YAML frontmatter and Markdown instructions. Skills can also bundle scripts, reference materials, templates, and assets.

**Skills are NOT prompts.** Prompts are conversation-level, one-off instructions. Skills are reusable, filesystem-based packages that load on demand and work across conversations and products.

## Three-tier progressive disclosure

| Tier | What loads | When | Token cost |
|------|-----------|------|------------|
| 1. Metadata | `name` + `description` | Agent startup | ~100 tokens/skill |
| 2. Instructions | Full SKILL.md body | When skill is triggered | <5000 tokens recommended |
| 3. Resources | Bundled scripts, references, assets | When instructions reference them | Effectively unlimited |

This means you can install dozens of skills with near-zero context penalty — only triggered skills consume meaningful context.

## Directory structure

```
skill-name/
├── SKILL.md           # Required: metadata + instructions
├── scripts/           # Optional: executable code
├── references/        # Optional: documentation
└── assets/            # Optional: templates, resources
```

Common installation path: `.agents/skills/` (cross-client convention).

## SKILL.md format

```yaml
---
name: skill-name            # Required. Max 64 chars, lowercase letters/numbers/hyphens
description: What it does. Use when [triggers].  # Required. Max 1024 chars
license: MIT                # Optional
compatibility: requires network access  # Optional
---
# Markdown body
```

**Critical**: The `description` field is the ONLY thing the agent sees at startup to decide whether to trigger the skill. It must include both what the skill does AND specific trigger conditions.

## Key design principles

- **Filesystem-native**: Skills are directories on disk. The agent reads SKILL.md via standard file operations — no special API needed.
- **Script execution**: When instructions reference a script, the agent runs it via bash. Only the script output enters context, not the code itself. This makes scripts far more token-efficient than generated code.
- **No practical size limit**: Bundled resources don't consume tokens until accessed. A skill can include extensive reference docs, schemas, etc.

## How agents discover and use skills

1. Agent starts → scans skill directories → loads all `name` + `description` into system prompt
2. User request matches a description → agent reads that skill's `SKILL.md` via bash/file-read
3. Instructions reference additional files → agent loads those as needed
4. Instructions mention scripts → agent executes scripts via bash, receives only output

## Differences across platforms

| Platform | Skill type | Sharing |
|----------|-----------|---------|
| Claude Code | Filesystem-based custom skills | Personal (~/.claude/skills/) or project (.claude/skills/) |
| Claude API | Pre-built + uploaded custom skills | Workspace-wide |
| Claude.ai | Pre-built + uploaded custom skills | Individual user only |

## Relationship to other concepts

- **AGENTS.md**: Project-level instructions, loaded unconditionally. Skills are task-specific and loaded on-demand.
- **MCP**: Connects agents to tools at runtime. Skills provide upfront knowledge and workflows.
- **A2A**: Agent-to-agent communication. Skills are internal capabilities an agent can use.

## References

- Specification: https://agentskills.io/specification
- Integration guide: https://agentskills.io/integrate-skills
- Example skills: https://github.com/anthropics/skills

# Skills Ecosystem Research

## Overview

The "skills" pattern is an [open standard developed by Anthropic](https://github.com/agentskills/agentskills) in late 2025 for packaging reusable capabilities for AI agents. Skills provide procedural knowledge through organized folders containing instructions, scripts, and resources that agents can discover and load dynamically.

## Core Concepts

### What is a Skill?

A skill is a directory containing a `SKILL.md` file with:
- **YAML frontmatter**: Metadata (name, description) pre-loaded into agent's system prompt
- **Markdown content**: Instructions, context, and examples loaded when relevant
- **Optional resources**: Additional files, scripts, or documentation referenced as needed

### Progressive Disclosure Pattern

Skills follow a three-level loading strategy:
1. **Metadata** (frontmatter): Always loaded - helps agent decide when to use the skill
2. **Core content** (SKILL.md): Loaded when skill is relevant to current task
3. **Additional files**: Loaded only as needed for complex scenarios

This design allows skills to contain effectively unbounded context without overwhelming the agent's context window.

### Distribution Model

The ecosystem uses a decentralized GitHub-based distribution:
- Skills are hosted in GitHub repositories
- Installation via CLI: `npx skills add <owner/repo>`
- Central discovery hub: [skills.sh](https://skills.sh)
- Specification: [github.com/agentskills/agentskills](https://github.com/agentskills/agentskills)

## Technical Implementation

### SKILL.md Format

```markdown
---
name: skill-name
description: |
  What this skill does and when agents should use it.
  Include keywords that help agents match the skill to relevant tasks.
---

# Skill Name

Instructions, context, and examples go here.

## When to Use
- Trigger condition 1
- Trigger condition 2

## How to Use
Step-by-step instructions...

## Examples
Concrete examples...
```

### Repository Structure

Skills can be organized as:
- **Single skill**: Repository root contains `SKILL.md`
- **Skill collection**: Multiple subdirectories, each with its own `SKILL.md`
- **Monorepo**: Skills organized under a `/skills` directory

### Installation Locations

Different agents use different paths:
- Claude Code: `~/.claude/skills`
- Codex CLI: `~/.codex/skills`
- Cursor: Project-level directories
- Kiro CLI: Project or global skills directories

The `npx skills` CLI handles these differences automatically.

## Ecosystem Adoption

### Compatible Platforms (40+)

Major adopters include:
- Anthropic (Claude Code, Claude.ai)
- OpenAI (ChatGPT, Codex CLI)
- Cursor
- GitHub Copilot
- Windsurf
- Kiro CLI
- Roo Code
- And [35+ more agents](https://skills.sh)

### Popular Skills

Top skills by installation count (from skills.sh):
1. **find-skills** (247K installs) - Skill discovery
2. **vercel-react-best-practices** (139K) - React optimization patterns
3. **web-design-guidelines** (105K) - UI/UX best practices
4. **remotion-best-practices** (95K) - Video generation
5. **frontend-design** (75K) - Frontend patterns

## Key Insights for Our Implementation

### 1. Simplicity is Critical
Skills are just folders with markdown files. No complex tooling, no compilation, no special formats. This makes them easy to create, audit, and maintain.

### 2. Metadata Drives Discovery
The description in frontmatter is crucial - it determines when agents load the skill. Good descriptions include:
- Domain/context
- Capabilities provided
- Trigger keywords

### 3. Content Organization Matters
- Keep core SKILL.md focused on common use cases
- Move specialized content to separate files
- Use clear section headers for agent navigation

### 4. Code as Both Tool and Documentation
Skills can include executable scripts that agents run directly, avoiding expensive token generation for algorithmic tasks.

### 5. Cross-Platform Compatibility
Following the standard ensures skills work across all compatible agents without modification.

## Comparison: Skills vs MCP Servers

| Aspect | Skills | MCP Servers |
|--------|--------|-------------|
| **Purpose** | Static procedural knowledge | Real-time tool access |
| **Format** | Markdown files | Running server process |
| **Use Case** | How to do things | Live data/integrations |
| **Complexity** | Simple (files) | Complex (servers) |
| **Relationship** | Complementary - often used together |

## Security Considerations

Skills provide agents with new capabilities through instructions and code. Best practices:
- Install only from trusted sources
- Audit skill contents before installation
- Review code dependencies and external connections
- Watch for obfuscated code or broad permissions

The open format (plain text files) makes auditing straightforward.

## References

Content was rephrased for compliance with licensing restrictions.

- [Skills.sh](https://skills.sh) - Primary distribution hub
- [Agent Skills Overview](https://inference.sh/blog/skills/agent-skills-overview) - Comprehensive technical guide
- [Vercel Labs Skills CLI](https://github.com/vercel-labs/skills) - Reference implementation
- [Agent Skills Specification](https://github.com/agentskills/agentskills) - Official standard
- [AgentSkills.io](https://agentskills.io) - Specification documentation

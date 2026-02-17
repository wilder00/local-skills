# Skill Creation Guide

This guide explains how to create new skills for this repository following the Agent Skills specification.

## Quick Start

1. Create a new directory under `/skills` with a kebab-case name
2. Add a `SKILL.md` file with proper frontmatter
3. Write clear, actionable content
4. Test with at least one agent
5. Update the main README

## SKILL.md Template

```markdown
---
name: skill-name
description: |
  Brief description of what this skill teaches.
  Include trigger keywords that help agents identify when to use this skill.
  Keep it under 3-4 lines.
---

# Skill Name

## Overview
Brief explanation of the concept (2-3 paragraphs).

## When to Use
- Specific scenario 1
- Specific scenario 2
- Specific scenario 3

## Core Principles
Key tenets of this concept:
1. Principle 1
2. Principle 2
3. Principle 3

## Implementation Guidelines

### Step 1: [Action]
Explanation...

### Step 2: [Action]
Explanation...

### Step 3: [Action]
Explanation...

## Examples

### Good Example
\`\`\`typescript
// Code demonstrating the principle
function goodExample() {
  // Clear, well-structured code
}
\`\`\`

**Why this works**: Explanation of what makes this good.

### Anti-Pattern
\`\`\`typescript
// Code violating the principle
function badExample() {
  // Problematic code
}
\`\`\`

**Why this fails**: Explanation of the problems.

## Common Pitfalls
- Pitfall 1 and how to avoid it
- Pitfall 2 and how to avoid it

## Related Concepts
- [Related Skill 1](../related-skill-1/SKILL.md)
- [Related Skill 2](../related-skill-2/SKILL.md)

## References
- The Pragmatic Programmer (Hunt & Thomas)
- Additional resources if applicable
```

## Frontmatter Requirements

### Required Fields

```yaml
---
name: skill-name
description: |
  Multi-line description.
  Include trigger keywords.
---
```

### Optional Fields

```yaml
---
name: skill-name
description: |
  Multi-line description.
internal: false              # Set to true to hide from public listing
allowed-tools:               # Restrict which tools agent can use
  - fs_read
  - fs_write
context: default             # or "fork" for isolated context (Claude Code only)
---
```

## Writing Effective Descriptions

The description is critical - it determines when agents load your skill.

### Good Description
```yaml
description: |
  Apply the DRY (Don't Repeat Yourself) principle to eliminate code duplication.
  Use when refactoring repeated logic, creating abstractions, or reviewing code
  for maintainability issues. Triggers: duplication, repeated code, copy-paste.
```

**Why it works**:
- Explains what the skill does
- Lists when to use it
- Includes trigger keywords
- Concise but informative

### Poor Description
```yaml
description: |
  This skill is about DRY.
```

**Why it fails**:
- Too vague
- No trigger keywords
- Doesn't explain when to use it

## Content Guidelines

### Structure
- **Start broad, get specific**: Overview → Principles → Implementation → Examples
- **Use progressive disclosure**: Core content in SKILL.md, details in separate files if needed
- **Be actionable**: Focus on what to do, not just theory

### Tone
- **Direct and practical**: Avoid academic language
- **Imperative mood**: "Use X when Y" not "X can be used when Y"
- **Confident but not dogmatic**: "Prefer X" not "Always use X"

### Code Examples
- **Show both good and bad**: Contrast helps learning
- **Explain why**: Don't just show code, explain the reasoning
- **Keep it simple**: Examples should illustrate one concept clearly
- **Use realistic scenarios**: Avoid contrived examples

### Length
- **SKILL.md**: 200-500 lines for most skills
- **If longer**: Split into multiple files
- **If shorter**: Might be too simple to be a skill

## File Organization

### Simple Skill (Most Common)
```
skill-name/
└── SKILL.md
```

### Complex Skill
```
skill-name/
├── SKILL.md                 # Core content
├── examples/
│   ├── typescript.md        # Language-specific examples
│   ├── python.md
│   └── rust.md
├── scripts/
│   └── analyze.py           # Executable tools
└── resources/
    └── advanced-topics.md   # Deep dives
```

## Testing Your Skill

### Manual Testing

1. **Install locally**:
   ```bash
   npx skills add ./local-skills --skill your-skill-name -a claude-code
   ```

2. **Test with agent**:
   - Open Claude Code (or your preferred agent)
   - Ask a question that should trigger your skill
   - Verify the agent loads and applies the skill correctly

3. **Check for issues**:
   - Does the agent understand when to use it?
   - Is the guidance clear and actionable?
   - Are the examples helpful?

### Validation Checklist

- [ ] YAML frontmatter is valid
- [ ] `name` matches directory name
- [ ] `description` includes trigger keywords
- [ ] Content follows template structure
- [ ] Code examples are correct and tested
- [ ] Markdown renders properly
- [ ] Links to related skills work
- [ ] No spelling or grammar errors
- [ ] Tested with at least one agent

## Common Mistakes

### 1. Vague Descriptions
❌ "This skill helps with code quality"  
✅ "Apply defensive programming techniques to handle errors and validate inputs"

### 2. Missing Trigger Keywords
❌ Description doesn't mention when to use the skill  
✅ Include keywords like "refactoring", "error handling", "testing"

### 3. Too Much Theory
❌ Long academic explanations  
✅ Brief overview, then practical implementation

### 4. No Examples
❌ Only text explanations  
✅ Include code examples showing good and bad patterns

### 5. Inconsistent Naming
❌ Directory: `DRY_Principle`, File: `skill.md`  
✅ Directory: `dry-principle`, File: `SKILL.md`

## Naming Conventions

### Directory Names
- **Kebab-case**: `dry-principle`, `tracer-bullets`
- **Descriptive**: Name should clearly indicate the concept
- **No abbreviations**: `design-by-contract` not `dbc`
- **No spaces**: Use hyphens

### Skill Names (in frontmatter)
- **Match directory**: If directory is `dry-principle`, name is `dry-principle`
- **Lowercase**: Consistent with directory naming
- **Hyphens**: Not underscores or spaces

## Version Control

### Committing New Skills
```bash
git add skills/new-skill/
git commit -m "Add new-skill: Brief description"
```

### Updating Existing Skills
```bash
git add skills/existing-skill/SKILL.md
git commit -m "Update existing-skill: What changed"
```

### Tagging Releases
```bash
# After adding/updating skills
git tag -a v1.1.0 -m "Add new-skill and update existing-skill"
git push origin v1.1.0
```

## Publishing Workflow

1. **Create skill** following this guide
2. **Test locally** with multiple agents
3. **Update README** to list new skill
4. **Update CHANGELOG** with changes
5. **Commit and push** to repository
6. **Tag release** with semantic version
7. **Announce** (optional) on social media, Discord, etc.

## Getting Help

If you're unsure about:
- **Skill structure**: Review existing skills in `/skills`
- **Frontmatter format**: Check [Agent Skills spec](https://github.com/agentskills/agentskills)
- **Content style**: Read ARCHITECTURE.md for guidelines
- **Technical issues**: Open an issue on GitHub

## Resources

- [Agent Skills Specification](https://github.com/agentskills/agentskills)
- [Vercel Labs Skills](https://github.com/vercel-labs/agent-skills) - Examples
- [Skills.sh](https://skills.sh) - Browse popular skills
- [The Pragmatic Programmer](https://pragprog.com/titles/tpp20/) - Source material

## Quick Reference

```bash
# Create new skill directory
mkdir skills/new-skill

# Copy template
cp docs/templates/SKILL.md skills/new-skill/

# Edit the skill
$EDITOR skills/new-skill/SKILL.md

# Test installation
npx skills add . --skill new-skill -a claude-code

# Commit
git add skills/new-skill/
git commit -m "Add new-skill"

# Tag release
git tag -a v1.1.0 -m "Add new-skill"
git push origin main --tags
```

---

**Remember**: The best skills are simple, actionable, and well-tested. Start small and iterate based on feedback.

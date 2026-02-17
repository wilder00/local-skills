# Rule Creation Guide

This guide explains how to add new rules to the pragmatic-programmer skill following the Vercel pattern.

## Quick Start

1. Create a new file in `skills/pragmatic-programmer/rules/`
2. Add frontmatter with title, impact, and tags
3. Write clear, actionable content with TypeScript examples
4. Update the main SKILL.md to reference the new rule
5. Test with at least one agent

## Rule File Template

```markdown
---
title: Rule Name
impact: HIGH
tags: keyword1, keyword2, keyword3
---

## Rule Name

## Overview
Brief explanation of the principle (2-3 paragraphs).

## When to Use
- Specific scenario 1
- Specific scenario 2
- Specific scenario 3

## Examples

### Anti-Pattern: Description

\`\`\`typescript
// Bad code example
\`\`\`

**Why this fails**: Explanation of the problems.

### Good Example: Description

\`\`\`typescript
// Good code example
\`\`\`

**Why this works**: Explanation of what makes this good.

## Common Pitfalls
- Pitfall 1 and how to avoid it
- Pitfall 2 and how to avoid it

## Related Concepts
- [Related Rule 1](./related-rule-1.md)
- [Related Rule 2](./related-rule-2.md)

## References
- Source material references
```

## Frontmatter Requirements

### Required Fields

```yaml
---
title: Human-Readable Rule Name
impact: CRITICAL | HIGH | MEDIUM | LOW
tags: keyword1, keyword2, keyword3
---
```

### Impact Levels

- **CRITICAL**: Major performance or correctness issues
- **HIGH**: Significant maintainability or robustness improvements
- **MEDIUM**: Moderate improvements to code quality
- **LOW**: Minor optimizations or advanced patterns

## Writing Effective Titles and Tags

### Good Title
```yaml
title: Don't Repeat Yourself (DRY)
```

**Why it works**:
- Clear and descriptive
- Includes acronym if applicable
- Human-readable

### Good Tags
```yaml
tags: duplication, abstraction, maintainability, refactoring
```

**Why it works**:
- Relevant keywords
- Helps agents find the rule
- Covers different aspects

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
- **Rule file**: 200-500 lines for most rules
- **If longer**: Consider splitting concepts
- **If shorter**: Might need more examples or detail

## File Organization

### Simple Rule (Most Common)
```
rules/
└── new-rule.md
```

### Update Main SKILL.md
Add reference to the new rule in the quick reference section.

## Testing Your Rule

### Manual Testing

1. **Install locally**:
   ```bash
   npx skills add ./local-skills -a claude-code
   ```

2. **Test with agent**:
   - Open Claude Code (or your preferred agent)
   - Ask a question that should trigger your rule
   - Verify the agent loads and applies the rule correctly

3. **Check for issues**:
   - Does the agent understand when to use it?
   - Is the guidance clear and actionable?
   - Are the examples helpful?

### Validation Checklist

- [ ] YAML frontmatter is valid
- [ ] `title` is clear and descriptive
- [ ] `impact` level is appropriate
- [ ] `tags` are relevant
- [ ] Content follows template structure
- [ ] Code examples are correct and tested
- [ ] Markdown renders properly
- [ ] Links to related rules work
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

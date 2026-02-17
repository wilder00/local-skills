# Architecture Proposal: Pragmatic Programmer Skills

## Executive Summary

This document proposes a minimal, standards-compliant implementation of a skills repository based on "The Pragmatic Programmer" principles. The architecture follows the [Agent Skills open standard](https://github.com/agentskills/agentskills) to ensure compatibility with 40+ AI coding agents.

## Design Philosophy

### Principles
1. **Standards Compliance**: Follow the Agent Skills specification exactly
2. **Simplicity First**: No unnecessary tooling or abstraction
3. **Developer Experience**: Easy to create, install, and maintain skills
4. **Future-Proof**: Extensible without breaking changes

### Anti-Patterns to Avoid
- Custom formats that deviate from the standard
- Complex build processes or compilation steps
- Tight coupling to specific agents or platforms
- Over-engineering the initial implementation

## Proposed Architecture

### Repository Structure

```
local-skills/
├── docs/
│   ├── RESEARCH.md              # Ecosystem research findings
│   ├── ARCHITECTURE.md          # This document
│   └── SKILL_CREATION_GUIDE.md  # How to create new skills
├── skills/
│   ├── dry-principle/
│   │   └── SKILL.md
│   ├── orthogonality/
│   │   └── SKILL.md
│   ├── tracer-bullets/
│   │   └── SKILL.md
│   ├── design-by-contract/
│   │   └── SKILL.md
│   ├── defensive-programming/
│   │   └── SKILL.md
│   ├── refactoring-early/
│   │   └── SKILL.md
│   ├── prototype-to-learn/
│   │   └── SKILL.md
│   └── pragmatic-paranoia/
│       └── SKILL.md
├── package.json                 # Metadata for npm distribution
├── README.md                    # User-facing documentation
└── .gitignore
```

### Skill Naming Convention

- **Kebab-case**: `dry-principle`, `tracer-bullets`
- **Descriptive**: Name should indicate the concept
- **Consistent**: Follow same pattern across all skills

### SKILL.md Template

```markdown
---
name: skill-name
description: |
  Brief description of the principle/concept.
  Include trigger keywords that help agents identify when to use this skill.
---

# Skill Name

## Overview
Brief explanation of the concept from The Pragmatic Programmer.

## When to Use
- Specific scenario 1
- Specific scenario 2
- Specific scenario 3

## Core Principles
Key tenets of this concept.

## Implementation Guidelines
Practical steps for applying this principle.

## Examples

### Good Example
```language
// Code demonstrating the principle
```

### Anti-Pattern
```language
// Code violating the principle
```

## Related Concepts
- Link to related skills
- External references

## References
- The Pragmatic Programmer (Hunt & Thomas)
```

## Initial Skill Set

Based on "The Pragmatic Programmer", the first release should include:

### Core Principles (Priority 1)
1. **DRY Principle** - Don't Repeat Yourself
2. **Orthogonality** - Decoupled, independent components
3. **Reversibility** - Avoid irreversible decisions
4. **Tracer Bullets** - Incremental development with immediate feedback

### Development Practices (Priority 2)
5. **Design by Contract** - Preconditions, postconditions, invariants
6. **Defensive Programming** - Assertive programming and error handling
7. **Refactoring Early** - Continuous code improvement
8. **Prototype to Learn** - Exploratory coding

### Quality & Testing (Priority 3)
9. **Pragmatic Paranoia** - Defensive coding mindset
10. **Test-Driven Development** - Write tests first
11. **Code That's Easy to Test** - Testability as a design goal
12. **Ruthless Testing** - Comprehensive testing strategies

## Technical Decisions

### 1. Distribution Method

**Decision**: Use GitHub + npm for distribution

**Rationale**:
- GitHub is the standard for skills hosting
- npm enables `npx skills add <owner/repo>` workflow
- No custom infrastructure needed
- Version control built-in

**Implementation**:
```json
{
  "name": "@<username>/pragmatic-programmer-skills",
  "version": "1.0.0",
  "description": "Agent skills based on The Pragmatic Programmer",
  "repository": {
    "type": "git",
    "url": "https://github.com/<username>/local-skills"
  }
}
```

### 2. Skill Format

**Decision**: Pure markdown with YAML frontmatter (no extensions)

**Rationale**:
- Follows Agent Skills specification exactly
- Maximum compatibility across agents
- Easy to read and edit
- No build step required

**Trade-offs**:
- Limited to markdown capabilities
- No dynamic content generation
- Manual updates required

### 3. Code Examples

**Decision**: Include inline code examples in SKILL.md

**Rationale**:
- Keeps everything in one file for simple skills
- Easier for agents to parse
- Follows progressive disclosure pattern

**When to Split**:
- If examples exceed ~200 lines
- If multiple language implementations needed
- If executable scripts are required

### 4. Language Agnostic vs Specific

**Decision**: Start language-agnostic, add language-specific variants later

**Rationale**:
- Principles apply across languages
- Broader applicability
- Easier to maintain initially

**Future Extension**:
```
skills/
├── dry-principle/
│   ├── SKILL.md           # Language-agnostic
│   ├── typescript.md      # TypeScript-specific
│   ├── python.md          # Python-specific
│   └── rust.md            # Rust-specific
```

### 5. Versioning Strategy

**Decision**: Semantic versioning with git tags

**Rationale**:
- Standard practice for npm packages
- Users can pin to specific versions
- Clear upgrade path

**Format**: `v1.0.0`, `v1.1.0`, `v2.0.0`

## Installation Workflow

### User Experience

```bash
# Install all skills
npx skills add <username>/local-skills

# Install specific skill
npx skills add <username>/local-skills --skill dry-principle

# Install to specific agent
npx skills add <username>/local-skills -a claude-code

# Install globally
npx skills add <username>/local-skills -g
```

### Behind the Scenes

1. CLI clones/downloads repository
2. Discovers all `SKILL.md` files in `/skills` directory
3. Prompts user for installation preferences
4. Copies or symlinks skills to agent-specific directories
5. Reports success and usage instructions

## Maintenance & Evolution

### Adding New Skills

1. Create new directory under `/skills`
2. Write `SKILL.md` following template
3. Test with at least one agent
4. Update README with new skill
5. Commit and tag new version

### Updating Existing Skills

1. Modify `SKILL.md` content
2. Increment version number
3. Document changes in CHANGELOG
4. Tag release

### Quality Assurance

- Manual review of all skills before release
- Test installation with multiple agents
- Validate YAML frontmatter syntax
- Check markdown rendering
- Verify code examples are correct

## Future Enhancements

### Phase 2: Enhanced Skills
- Language-specific implementations
- Executable scripts for code analysis
- Multi-file skills with additional resources
- Interactive examples

### Phase 3: Tooling
- Skill validation CLI
- Automated testing framework
- Skill generator/scaffolding tool
- Documentation site

### Phase 4: Community
- Contribution guidelines
- Skill request process
- Community-submitted skills
- Usage analytics (opt-in)

## Open Questions for Clarification

Before proceeding with implementation, please clarify:

### 1. Tech Stack
- **TypeScript or JavaScript?** For any tooling/scripts we might add
- **Node.js version target?** (Recommend: Node 18+ for modern features)

### 2. Repository Naming
- **GitHub username/org?** Needed for package naming
- **Repository name preference?** Current: `local-skills`, or something like `pragmatic-programmer-skills`?

### 3. Scope
- **All 70+ tips from the book?** Or focus on ~10-15 core principles?
- **Include code examples?** If yes, which languages to prioritize?

### 4. Licensing
- **License preference?** (Recommend: MIT for maximum compatibility)

### 5. Branding
- **Reference "The Pragmatic Programmer" directly?** May have trademark considerations
- **Alternative naming?** e.g., "pragmatic-coding-principles"

## Recommended Next Steps

1. **Clarify open questions** (see above)
2. **Create initial repository structure** (folders, README, package.json)
3. **Implement 3-5 pilot skills** to validate approach
4. **Test installation** with 2-3 different agents
5. **Iterate based on feedback**
6. **Complete remaining skills**
7. **Publish to GitHub and npm**

## Success Criteria

- ✅ Skills install successfully via `npx skills add`
- ✅ Compatible with Claude Code, Cursor, and GitHub Copilot
- ✅ Skills load correctly in agent context
- ✅ Agents can apply principles when prompted
- ✅ Documentation is clear and complete
- ✅ Repository follows open source best practices

## Conclusion

This architecture provides a minimal, standards-compliant foundation for distributing Pragmatic Programmer principles as agent skills. By following the established Agent Skills specification and keeping the implementation simple, we ensure maximum compatibility and maintainability while leaving room for future enhancements.

The proposed structure balances immediate usability with long-term extensibility, following the very principles we're trying to teach.

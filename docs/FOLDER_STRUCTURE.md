# Proposed Folder Structure

## Overview

This document presents the complete folder structure for the Pragmatic Programmer Skills repository, designed to support the `npx skills add` workflow.

## Complete Structure

```
local-skills/
│
├── docs/                                    # Developer documentation (not installed)
│   ├── RESEARCH.md                          # Skills ecosystem research
│   ├── ARCHITECTURE.md                      # Technical architecture decisions
│   ├── SKILL_CREATION_GUIDE.md             # How to create new rules
│   └── CONTRIBUTING.md                      # Contribution guidelines (future)
│
├── skills/                                  # Installable skills directory
│   └── pragmatic-programmer/                # Single skill (Vercel pattern)
│       ├── SKILL.md                         # Main skill overview
│       └── rules/                           # Individual rules
│           ├── dry-principle.md
│           ├── orthogonality.md
│           ├── reversibility.md
│           ├── tracer-bullets.md
│           ├── design-by-contract.md
│           ├── defensive-programming.md
│           ├── refactoring-early.md
│           ├── prototype-to-learn.md
│           ├── pragmatic-paranoia.md
│           ├── test-driven-development.md
│           ├── code-that-tests-easy.md
│           └── ruthless-testing.md
│
├── .github/                                 # GitHub configuration (future)
│   ├── workflows/
│   │   └── validate-skills.yml             # CI to validate SKILL.md files
│   └── ISSUE_TEMPLATE/
│       └── rule-request.md                 # Template for requesting new rules
│
├── package.json                             # npm package metadata
├── README.md                                # User-facing documentation
├── LICENSE                                  # License file (MIT)
├── CHANGELOG.md                             # Version history
└── .gitignore                               # Git ignore rules
```

## Directory Purposes

### `/docs` - Developer Documentation
**Purpose**: Internal documentation for maintainers and contributors  
**Not Installed**: This directory is excluded from skill installation  
**Contents**:
- Research findings
- Architecture decisions
- Creation guides
- Contribution guidelines

### `/skills` - Installable Skills
**Purpose**: The core skill that agents will load  
**Installed**: SKILL.md and rules/ directory  
**Structure**: Single skill with multiple rules (Vercel pattern)

### `/.github` - Repository Automation
**Purpose**: GitHub-specific configuration  
**Future Enhancement**: CI/CD and issue templates  
**Optional**: Can be added in Phase 2

## Skill Directory Pattern

The skill follows Vercel's pattern:

```
pragmatic-programmer/
├── SKILL.md                    # Required: Main skill overview
└── rules/                      # Required: Individual rules
    ├── dry-principle.md
    ├── orthogonality.md
    └── ... (10 more)
```

Each rule file contains:
- YAML frontmatter (title, impact, tags)
- Overview and explanation
- TypeScript examples (good and bad)
- Common pitfalls
- Related concepts

## File Naming Conventions

### Directories
- **Kebab-case**: `dry-principle`, `tracer-bullets`
- **Descriptive**: Name clearly indicates the concept
- **No spaces**: Ensures cross-platform compatibility

### Files
- **SKILL.md**: Always uppercase (specification requirement)
- **Other files**: lowercase with hyphens

## Installation Behavior

When a user runs `npx skills add wilder00/local-skills`:

1. **Discovery**: CLI scans `/skills` directory
2. **Detection**: Finds pragmatic-programmer skill
3. **Presentation**: Shows skill overview
4. **Installation**: Copies/symlinks to agent-specific directories

### Example Installation Output

```
Found 1 skill in wilder00/local-skills:

  ✓ pragmatic-programmer (12 rules)

? Which agents should receive this skill?
  ◉ Claude Code
  ◉ Cursor
  ◯ GitHub Copilot
  ◯ All agents

Installing to:
  → ~/.claude/skills/
  → ~/.cursor/skills/

✓ Successfully installed pragmatic-programmer
```

## Excluded from Installation

The following are NOT installed as skills:
- `/docs` directory
- `package.json`
- `README.md`
- `LICENSE`
- `.github` directory
- Any dotfiles (`.gitignore`, etc.)

## Scalability Considerations

### Current Structure (12 skills)
- Simple flat structure under `/skills`
- Easy to navigate and maintain
- No categorization needed

### Future Growth (50+ skills)
Consider categorizing:

```
skills/
├── core-principles/
│   ├── dry-principle/
│   ├── orthogonality/
│   └── reversibility/
├── development-practices/
│   ├── tracer-bullets/
│   ├── prototype-to-learn/
│   └── refactoring-early/
└── testing/
    ├── test-driven-development/
    ├── code-that-tests-easy/
    └── ruthless-testing/
```

**Note**: The `npx skills` CLI handles nested structures automatically.

## Validation Rules

Each skill directory must:
- ✅ Contain a `SKILL.md` file
- ✅ Have valid YAML frontmatter with `name` and `description`
- ✅ Use kebab-case naming
- ✅ Be self-contained (no external dependencies)

## Next Steps

Once you approve this structure, I will:

1. ✅ Create the folder structure
2. ✅ Generate `package.json` with proper metadata
3. ✅ Write a comprehensive `README.md`
4. ✅ Create a `SKILL.md` template
5. ✅ Implement 2-3 pilot skills to validate the approach
6. ⏸️ Wait for your feedback before completing all skills

## Questions for Approval

Before generating the boilerplate:

1. **Approve this structure?** Any changes needed?
2. **Initial skill count?** Start with 5, 10, or all 12?
3. **Language for examples?** TypeScript, Python, language-agnostic?
4. **Repository name?** Keep `local-skills` or rename?
5. **Your GitHub username?** Needed for package.json

Please review and let me know if you'd like any modifications to this structure.

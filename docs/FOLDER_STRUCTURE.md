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
│   ├── SKILL_CREATION_GUIDE.md             # How to create new skills
│   └── CONTRIBUTING.md                      # Contribution guidelines (future)
│
├── skills/                                  # Installable skills directory
│   │
│   ├── dry-principle/                       # Don't Repeat Yourself
│   │   └── SKILL.md
│   │
│   ├── orthogonality/                       # Decoupled components
│   │   └── SKILL.md
│   │
│   ├── reversibility/                       # Avoid irreversible decisions
│   │   └── SKILL.md
│   │
│   ├── tracer-bullets/                      # Incremental development
│   │   └── SKILL.md
│   │
│   ├── design-by-contract/                  # Contracts and assertions
│   │   └── SKILL.md
│   │
│   ├── defensive-programming/               # Assertive programming
│   │   └── SKILL.md
│   │
│   ├── refactoring-early/                   # Continuous improvement
│   │   └── SKILL.md
│   │
│   ├── prototype-to-learn/                  # Exploratory coding
│   │   └── SKILL.md
│   │
│   ├── pragmatic-paranoia/                  # Defensive mindset
│   │   └── SKILL.md
│   │
│   ├── test-driven-development/             # TDD practices
│   │   └── SKILL.md
│   │
│   ├── code-that-tests-easy/                # Testability design
│   │   └── SKILL.md
│   │
│   └── ruthless-testing/                    # Comprehensive testing
│       └── SKILL.md
│
├── .github/                                 # GitHub configuration (future)
│   ├── workflows/
│   │   └── validate-skills.yml             # CI to validate SKILL.md files
│   └── ISSUE_TEMPLATE/
│       └── skill-request.md                # Template for requesting new skills
│
├── package.json                             # npm package metadata
├── README.md                                # User-facing documentation
├── LICENSE                                  # License file (MIT recommended)
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
**Purpose**: The core skills that agents will load  
**Installed**: All subdirectories with SKILL.md files  
**Structure**: Each skill is a self-contained directory

### `/.github` - Repository Automation
**Purpose**: GitHub-specific configuration  
**Future Enhancement**: CI/CD and issue templates  
**Optional**: Can be added in Phase 2

## Skill Directory Pattern

Each skill follows this pattern:

```
skill-name/
├── SKILL.md                    # Required: Main skill content
├── examples/                   # Optional: Extended examples
│   ├── good-example.ts
│   └── anti-pattern.ts
├── scripts/                    # Optional: Executable tools
│   └── analyze.py
└── resources/                  # Optional: Additional docs
    └── references.md
```

**For initial release**: Keep it simple with just `SKILL.md` per skill.

## File Naming Conventions

### Directories
- **Kebab-case**: `dry-principle`, `tracer-bullets`
- **Descriptive**: Name clearly indicates the concept
- **No spaces**: Ensures cross-platform compatibility

### Files
- **SKILL.md**: Always uppercase (specification requirement)
- **Other files**: lowercase with hyphens

## Installation Behavior

When a user runs `npx skills add <owner>/local-skills`:

1. **Discovery**: CLI scans `/skills` directory
2. **Detection**: Finds all directories containing `SKILL.md`
3. **Presentation**: Shows list of available skills
4. **Selection**: User chooses which skills to install
5. **Installation**: Copies/symlinks to agent-specific directories

### Example Installation Output

```
Found 12 skills in <owner>/local-skills:

  ✓ dry-principle
  ✓ orthogonality
  ✓ reversibility
  ✓ tracer-bullets
  ✓ design-by-contract
  ✓ defensive-programming
  ✓ refactoring-early
  ✓ prototype-to-learn
  ✓ pragmatic-paranoia
  ✓ test-driven-development
  ✓ code-that-tests-easy
  ✓ ruthless-testing

? Which skills would you like to install? (Use arrow keys and space to select)
  ◉ All skills
  ◯ Select specific skills

? Which agents should receive these skills?
  ◉ Claude Code
  ◉ Cursor
  ◯ GitHub Copilot
  ◯ All agents

Installing to:
  → ~/.claude/skills/
  → ~/.cursor/skills/

✓ Successfully installed 12 skills
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

# Quick Start Guide

## For Users

### Install All Skills
```bash
npx skills add wilder00/local-skills
```

### Install Specific Skill
```bash
npx skills add wilder00/local-skills --skill dry-principle
```

### Install to Specific Agent
```bash
npx skills add wilder00/local-skills -a claude-code
```

### Use Skills
Once installed, just ask your AI agent:
- "Review this code for DRY violations"
- "Help me refactor this to be more orthogonal"
- "Apply defensive programming to this function"
- "Write tests for this using TDD"

## For Maintainers

### Publish to GitHub

```bash
# 1. Initialize repository
cd /home/wilder/temp/local-skills
git init
git add .
git commit -m "Initial commit: 12 pragmatic programming skills"

# 2. Create repository on GitHub
# Go to https://github.com/new
# Name: local-skills
# Public repository

# 3. Push to GitHub
git remote add origin https://github.com/wilder00/local-skills.git
git branch -M main
git push -u origin main

# 4. Tag release
git tag -a v1.0.0 -m "Release version 1.0.0"
git push origin v1.0.0
```

### Add New Skill

```bash
# 1. Create directory
mkdir skills/new-skill

# 2. Create SKILL.md
cat > skills/new-skill/SKILL.md << 'EOF'
---
name: new-skill
description: |
  Brief description with trigger keywords.
---

# New Skill

## Overview
...

## When to Use
...

## Examples
...
EOF

# 3. Update README.md
# Add skill to the list

# 4. Update CHANGELOG.md
# Document the addition

# 5. Commit and tag
git add skills/new-skill/
git commit -m "Add new-skill"
git tag -a v1.1.0 -m "Add new-skill"
git push origin main --tags
```

### Test Locally

```bash
# Install from local directory
npx skills add . --skill dry-principle -a claude-code

# Verify installation
npx skills list
```

## Documentation

- **README.md** - User documentation
- **docs/ARCHITECTURE.md** - Technical design
- **docs/SKILL_CREATION_GUIDE.md** - How to create skills
- **docs/RESEARCH.md** - Ecosystem analysis

## Support

- Issues: https://github.com/wilder00/local-skills/issues
- Discussions: https://github.com/wilder00/local-skills/discussions

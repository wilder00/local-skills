# Local Skills

Agent skills for pragmatic software development principles. Compatible with Claude Code, Cursor, GitHub Copilot, and 40+ other AI coding agents.

## Installation

```bash
npx skills add wilder00/local-skills
```

### Install to Specific Agent

```bash
npx skills add wilder00/local-skills -a claude-code
```

## Available Skills

### Pragmatic Programmer

Comprehensive guide with 12 rules across 3 categories:

**Core Principles (4 rules)**
- Don't Repeat Yourself (DRY)
- Orthogonality
- Reversibility
- Tracer Bullets

**Development Practices (4 rules)**
- Design by Contract
- Defensive Programming
- Refactoring Early
- Prototype to Learn

**Quality & Testing (4 rules)**
- Pragmatic Paranoia
- Test-Driven Development
- Code That Tests Easy
- Ruthless Testing

## Usage

Once installed, the skill is automatically available to your AI agent. The agent will load relevant rules based on your coding context.

Example prompts:
- "Review this code for DRY violations"
- "Help me refactor this to be more orthogonal"
- "Apply defensive programming to this function"
- "Write tests for this using TDD"

## Compatible Agents

- Claude Code
- Cursor
- GitHub Copilot
- Windsurf
- Kiro CLI
- Roo Code
- [And 35+ more](https://skills.sh)

## Development

See [docs/](./docs) for architecture, research, and contribution guidelines.

## License

MIT

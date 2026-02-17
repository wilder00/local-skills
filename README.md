# Local Skills

Agent skills for pragmatic software development principles. Compatible with Claude Code, Cursor, GitHub Copilot, and 40+ other AI coding agents.

## Installation

```bash
npx skills add wilder00/local-skills
```

### Install Specific Skills

```bash
npx skills add wilder00/local-skills --skill dry-principle
```

### Install to Specific Agent

```bash
npx skills add wilder00/local-skills -a claude-code
```

## Available Skills

### Core Principles
- **dry-principle** - Don't Repeat Yourself
- **orthogonality** - Decoupled, independent components
- **reversibility** - Avoid irreversible decisions
- **tracer-bullets** - Incremental development with immediate feedback

### Development Practices
- **design-by-contract** - Preconditions, postconditions, and invariants
- **defensive-programming** - Assertive programming and error handling
- **refactoring-early** - Continuous code improvement
- **prototype-to-learn** - Exploratory coding for learning

### Quality & Testing
- **pragmatic-paranoia** - Defensive coding mindset
- **test-driven-development** - Write tests first
- **code-that-tests-easy** - Design for testability
- **ruthless-testing** - Comprehensive testing strategies

## Usage

Once installed, these skills are automatically available to your AI agent. The agent will load relevant skills based on your coding context and requests.

Example prompts:
- "Review this code for DRY violations"
- "Help me refactor this to be more orthogonal"
- "Apply defensive programming to this function"

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

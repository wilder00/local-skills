---
name: pragmatic-programmer
description: |
  Pragmatic software development principles for writing maintainable, robust code.
  Use when architecting systems, refactoring code, or reviewing for best practices.
  Triggers: code review, refactoring, design decisions, testing, architecture.
---

# Pragmatic Programmer

Comprehensive guide to pragmatic software development principles. Contains 12 rules across 3 categories, covering core principles, development practices, and quality assurance.

## When to Apply

Reference these guidelines when:
- Designing new systems or components
- Refactoring existing code
- Reviewing code for maintainability
- Making architectural decisions
- Writing tests or improving testability

## Rule Categories

| Category | Rules | Focus |
|----------|-------|-------|
| Core Principles | 4 | DRY, Orthogonality, Reversibility, Tracer Bullets |
| Development Practices | 4 | Contracts, Defensive Programming, Refactoring, Prototyping |
| Quality & Testing | 4 | Paranoia, TDD, Testability, Ruthless Testing |

## Quick Reference

### Core Principles
- `dry-principle` - Don't Repeat Yourself
- `orthogonality` - Decoupled, independent components
- `reversibility` - Avoid irreversible decisions
- `tracer-bullets` - Incremental development with feedback

### Development Practices
- `design-by-contract` - Preconditions, postconditions, invariants
- `defensive-programming` - Assertive programming and error handling
- `refactoring-early` - Continuous code improvement
- `prototype-to-learn` - Exploratory coding for learning

### Quality & Testing
- `pragmatic-paranoia` - Defensive coding mindset
- `test-driven-development` - Write tests first
- `code-that-tests-easy` - Design for testability
- `ruthless-testing` - Comprehensive testing strategies

## How to Use

Read individual rule files for detailed explanations and TypeScript examples:

```
rules/dry-principle.md
rules/orthogonality.md
rules/tracer-bullets.md
```

Each rule file contains:
- Overview of the principle
- When to use guidelines
- TypeScript code examples (good and bad)
- Common pitfalls
- Related concepts

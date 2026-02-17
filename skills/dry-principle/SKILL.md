---
name: dry-principle
description: |
  Apply the DRY (Don't Repeat Yourself) principle to eliminate code duplication.
  Use when refactoring repeated logic, creating abstractions, or reviewing code
  for maintainability. Triggers: duplication, repeated code, copy-paste, similar logic.
---

# DRY Principle

## Overview

Every piece of knowledge must have a single, unambiguous, authoritative representation within a system. The DRY principle isn't just about avoiding duplicate code—it's about avoiding duplicate knowledge. When you duplicate knowledge, you create maintenance nightmares where changes must be synchronized across multiple locations.

## When to Use

- You find yourself copying and pasting code
- Similar logic appears in multiple places
- Changes to one piece of code require changes elsewhere
- Business rules are scattered across the codebase
- Documentation repeats what the code says

## Core Principles

1. **Single Source of Truth**: Each piece of knowledge exists in exactly one place
2. **Abstraction Over Duplication**: Create reusable components instead of copying
3. **Knowledge, Not Just Code**: DRY applies to data, documentation, and configuration too

## Implementation Guidelines

### Identify Duplication

Look for:
- Identical or similar code blocks
- Repeated business logic
- Duplicated data structures
- Copy-pasted configuration

### Extract and Abstract

1. Identify the common pattern
2. Extract to a function, class, or module
3. Parameterize the differences
4. Replace duplicates with calls to the abstraction

### Maintain Single Source

- Use constants for magic numbers
- Centralize configuration
- Generate code from schemas
- Link documentation to code

## Examples

### Anti-Pattern: Duplicated Validation

```typescript
// User registration
function registerUser(email: string, password: string) {
  if (!email.includes('@')) {
    throw new Error('Invalid email');
  }
  if (password.length < 8) {
    throw new Error('Password too short');
  }
  // ... registration logic
}

// User update
function updateUserEmail(userId: string, email: string) {
  if (!email.includes('@')) {
    throw new Error('Invalid email');
  }
  // ... update logic
}

// Password reset
function resetPassword(email: string, newPassword: string) {
  if (!email.includes('@')) {
    throw new Error('Invalid email');
  }
  if (newPassword.length < 8) {
    throw new Error('Password too short');
  }
  // ... reset logic
}
```

**Why this fails**: Validation logic is duplicated. If requirements change (e.g., stronger email validation), you must update multiple places.

### Good Example: Single Source of Truth

```typescript
// Centralized validation
class Validator {
  static email(email: string): void {
    if (!email.match(/^[^\s@]+@[^\s@]+\.[^\s@]+$/)) {
      throw new Error('Invalid email format');
    }
  }

  static password(password: string): void {
    if (password.length < 8) {
      throw new Error('Password must be at least 8 characters');
    }
    if (!/[A-Z]/.test(password)) {
      throw new Error('Password must contain uppercase letter');
    }
  }
}

// Usage
function registerUser(email: string, password: string) {
  Validator.email(email);
  Validator.password(password);
  // ... registration logic
}

function updateUserEmail(userId: string, email: string) {
  Validator.email(email);
  // ... update logic
}

function resetPassword(email: string, newPassword: string) {
  Validator.email(email);
  Validator.password(newPassword);
  // ... reset logic
}
```

**Why this works**: Validation logic exists in one place. Changes propagate automatically to all usage sites.

### Anti-Pattern: Duplicated Data Transformation

```typescript
// In API handler
app.get('/users/:id', async (req, res) => {
  const user = await db.users.findById(req.params.id);
  res.json({
    id: user.id,
    name: user.name,
    email: user.email,
    createdAt: user.created_at.toISOString(),
  });
});

// In another handler
app.get('/users', async (req, res) => {
  const users = await db.users.findAll();
  res.json(users.map(user => ({
    id: user.id,
    name: user.name,
    email: user.email,
    createdAt: user.created_at.toISOString(),
  })));
});
```

**Why this fails**: Transformation logic is duplicated. Adding a field requires updating multiple handlers.

### Good Example: Centralized Transformation

```typescript
// Single transformation function
function serializeUser(user: DbUser): ApiUser {
  return {
    id: user.id,
    name: user.name,
    email: user.email,
    createdAt: user.created_at.toISOString(),
  };
}

// Usage
app.get('/users/:id', async (req, res) => {
  const user = await db.users.findById(req.params.id);
  res.json(serializeUser(user));
});

app.get('/users', async (req, res) => {
  const users = await db.users.findAll();
  res.json(users.map(serializeUser));
});
```

**Why this works**: Transformation logic is centralized. Schema changes happen in one place.

## Common Pitfalls

- **Over-abstraction**: Don't DRY up code that's coincidentally similar but represents different concepts
- **Premature abstraction**: Wait until you have 3+ instances before abstracting
- **Wrong abstraction**: A bad abstraction is worse than duplication—be willing to inline and try again
- **Ignoring non-code duplication**: DRY applies to configuration, data, and documentation too

## Related Concepts

- [Orthogonality](../orthogonality/SKILL.md) - Independent, decoupled components
- [Refactoring Early](../refactoring-early/SKILL.md) - Continuous improvement

## References

- The Pragmatic Programmer (Hunt & Thomas)
- Rule of Three: Abstract after third duplication

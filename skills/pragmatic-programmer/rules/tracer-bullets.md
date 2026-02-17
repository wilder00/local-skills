---
title: Tracer Bullets
impact: HIGH
tags: incremental, feedback, architecture, prototyping
---


## Tracer Bullets

## Overview

Tracer bullets are loaded at intervals alongside regular ammunition. When they're fired, their phosphorus ignites and leaves a visible trail from gun to target. Soldiers use them to refine their aim in real-time. In software, tracer bullet development means building a minimal end-to-end implementation first, then filling in the details. You get immediate feedback on whether your architecture works.

## When to Use

- Starting new projects with uncertain requirements
- Validating architectural decisions
- Integrating unfamiliar technologies
- Building systems with many unknowns
- Need to demonstrate progress quickly

## Core Principles

1. **End-to-End First**: Build a complete path through the system, however minimal
2. **Immediate Feedback**: See results quickly to validate assumptions
3. **Incremental Refinement**: Add features to the working skeleton
4. **Real Code**: Not throwaway prototypes—production-quality structure

## Implementation Guidelines

### Start with the Skeleton

1. Identify the critical path through your system
2. Build minimal implementation of each layer
3. Connect all components end-to-end
4. Verify the architecture works

### Iterate and Expand

- Add features incrementally
- Refine existing functionality
- Keep the system working at each step
- Adjust architecture based on feedback

### Maintain Visibility

- Deploy early and often
- Show progress to stakeholders
- Get user feedback quickly
- Adjust course as needed

## Examples

### Anti-Pattern: Big Bang Development

```typescript
// Month 1: Build entire database schema
CREATE TABLE users (
  id UUID PRIMARY KEY,
  email VARCHAR(255),
  password_hash VARCHAR(255),
  first_name VARCHAR(100),
  last_name VARCHAR(100),
  phone VARCHAR(20),
  address TEXT,
  city VARCHAR(100),
  state VARCHAR(50),
  zip VARCHAR(10),
  country VARCHAR(50),
  created_at TIMESTAMP,
  updated_at TIMESTAMP,
  last_login TIMESTAMP,
  is_verified BOOLEAN,
  verification_token VARCHAR(255),
  reset_token VARCHAR(255),
  preferences JSONB
);

// Month 2: Build complete authentication system
// Month 3: Build entire API layer
// Month 4: Build complete frontend
// Month 5: Try to integrate everything
// Month 6: Discover architecture doesn't work
```

**Why this fails**: No feedback until late in the project. Architectural problems discovered after significant investment. Requirements may have changed. No working system to show stakeholders.

### Good Example: Tracer Bullet Approach

```typescript
// Week 1: Minimal end-to-end user registration

// 1. Minimal database
CREATE TABLE users (
  id UUID PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL
);

// 2. Minimal API endpoint
app.post('/api/register', async (req, res) => {
  const { email, password } = req.body;
  const hash = await bcrypt.hash(password, 10);
  const user = await db.query(
    'INSERT INTO users (id, email, password_hash) VALUES ($1, $2, $3) RETURNING *',
    [crypto.randomUUID(), email, hash]
  );
  res.json({ id: user.id, email: user.email });
});

// 3. Minimal frontend form
function RegisterForm() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');

  const handleSubmit = async (e: FormEvent) => {
    e.preventDefault();
    await fetch('/api/register', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ email, password })
    });
  };

  return (
    <form onSubmit={handleSubmit}>
      <input value={email} onChange={e => setEmail(e.target.value)} />
      <input type="password" value={password} onChange={e => setPassword(e.target.value)} />
      <button type="submit">Register</button>
    </form>
  );
}

// Week 2: Add validation
// Week 3: Add email verification
// Week 4: Add user profile fields
// Each week: working system with more features
```

**Why this works**: Working end-to-end system from week 1. Immediate feedback on architecture. Can demonstrate progress. Easy to adjust based on feedback. Stakeholders see real functionality early.

### Anti-Pattern: Layer-by-Layer Development

```typescript
// Build entire data layer first
class UserRepository {
  async create(user: User): Promise<User> { /* ... */ }
  async findById(id: string): Promise<User | null> { /* ... */ }
  async findByEmail(email: string): Promise<User | null> { /* ... */ }
  async update(id: string, data: Partial<User>): Promise<User> { /* ... */ }
  async delete(id: string): Promise<void> { /* ... */ }
  async list(filters: UserFilters): Promise<User[]> { /* ... */ }
  async count(filters: UserFilters): Promise<number> { /* ... */ }
}

// Then build entire service layer
class UserService {
  // 20+ methods
}

// Then build entire API layer
class UserController {
  // 15+ endpoints
}

// Finally try to connect everything
// Discover mismatches between layers
```

**Why this fails**: No integration until the end. Layer interfaces may not match actual needs. Over-engineering features that won't be used. Can't test the full stack until everything is built.

### Good Example: Feature-by-Feature

```typescript
// Feature 1: User registration (all layers)
class UserRepository {
  async create(user: CreateUserInput): Promise<User> {
    // Minimal implementation
  }
}

class UserService {
  constructor(private repo: UserRepository) {}
  
  async register(email: string, password: string): Promise<User> {
    const hash = await bcrypt.hash(password, 10);
    return this.repo.create({ email, passwordHash: hash });
  }
}

app.post('/api/register', async (req, res) => {
  const user = await userService.register(req.body.email, req.body.password);
  res.json(user);
});

// Test end-to-end, then move to Feature 2: User login
class UserRepository {
  async create(user: CreateUserInput): Promise<User> { /* ... */ }
  async findByEmail(email: string): Promise<User | null> {
    // Add only what's needed for login
  }
}

class UserService {
  async register(email: string, password: string): Promise<User> { /* ... */ }
  
  async login(email: string, password: string): Promise<string> {
    const user = await this.repo.findByEmail(email);
    if (!user || !await bcrypt.compare(password, user.passwordHash)) {
      throw new Error('Invalid credentials');
    }
    return generateToken(user);
  }
}

// Each feature: complete vertical slice through all layers
```

**Why this works**: Each feature is fully integrated. Interfaces evolve based on real needs. Can deploy and test each feature independently. Architecture validated continuously.

## Common Pitfalls

- **Confusing with prototypes**: Tracer bullets are production code, not throwaway experiments
- **Skipping quality**: Don't sacrifice code quality for speed
- **Building too much**: Keep initial implementation truly minimal
- **Ignoring feedback**: The point is to learn and adjust

## Related Concepts

- [Prototype to Learn](../prototype-to-learn/SKILL.md) - Exploratory coding
- [Reversibility](../reversibility/SKILL.md) - Keep options open

## References

- The Pragmatic Programmer (Hunt & Thomas)
- Vertical Slice Architecture
- Walking Skeleton pattern

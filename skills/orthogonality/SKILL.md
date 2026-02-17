---
name: orthogonality
description: |
  Design orthogonal systems where components are independent and decoupled.
  Use when architecting systems, refactoring coupled code, or designing APIs.
  Triggers: tight coupling, cascading changes, hard to test, side effects.
---

# Orthogonality

## Overview

Two or more things are orthogonal if changes in one do not affect the others. In software, orthogonality means designing components that are independent—changing one doesn't require changing others. Orthogonal systems are easier to design, build, test, and extend.

## When to Use

- Designing new systems or modules
- Refactoring tightly coupled code
- Components have unexpected side effects
- Changes cascade through the system
- Testing requires complex setup

## Core Principles

1. **Independence**: Components operate without knowledge of others
2. **Single Responsibility**: Each component does one thing well
3. **Minimal Coupling**: Reduce dependencies between components
4. **Composability**: Combine components in different ways

## Implementation Guidelines

### Design for Independence

- Separate concerns into distinct modules
- Use interfaces to define contracts
- Avoid global state
- Pass dependencies explicitly

### Reduce Coupling

- Depend on abstractions, not implementations
- Use dependency injection
- Communicate through well-defined interfaces
- Avoid reaching into other components' internals

### Test for Orthogonality

If you can't test a component in isolation, it's not orthogonal.

## Examples

### Anti-Pattern: Tightly Coupled Components

```typescript
// Database logic mixed with business logic
class UserService {
  async createUser(email: string, password: string) {
    // Validation
    if (!email.includes('@')) throw new Error('Invalid email');
    
    // Database connection
    const connection = await mysql.createConnection({
      host: 'localhost',
      user: 'root',
      password: 'secret',
      database: 'myapp'
    });
    
    // Hashing
    const hashedPassword = crypto
      .createHash('sha256')
      .update(password)
      .digest('hex');
    
    // Insert
    await connection.execute(
      'INSERT INTO users (email, password) VALUES (?, ?)',
      [email, hashedPassword]
    );
    
    // Email notification
    await fetch('https://api.sendgrid.com/v3/mail/send', {
      method: 'POST',
      headers: { 'Authorization': `Bearer ${process.env.SENDGRID_KEY}` },
      body: JSON.stringify({
        to: email,
        subject: 'Welcome!',
        text: 'Thanks for signing up'
      })
    });
    
    await connection.end();
  }
}
```

**Why this fails**: Multiple concerns are tangled together. Testing requires a real database and email service. Changing email providers requires modifying UserService.

### Good Example: Orthogonal Components

```typescript
// Separate, independent components
interface UserRepository {
  create(user: User): Promise<User>;
}

interface EmailService {
  send(to: string, subject: string, body: string): Promise<void>;
}

interface PasswordHasher {
  hash(password: string): Promise<string>;
}

class UserService {
  constructor(
    private userRepo: UserRepository,
    private emailService: EmailService,
    private hasher: PasswordHasher
  ) {}

  async createUser(email: string, password: string): Promise<User> {
    const hashedPassword = await this.hasher.hash(password);
    const user = await this.userRepo.create({ email, password: hashedPassword });
    await this.emailService.send(email, 'Welcome!', 'Thanks for signing up');
    return user;
  }
}

// Implementations are separate and swappable
class MySQLUserRepository implements UserRepository {
  async create(user: User): Promise<User> {
    // MySQL-specific logic
  }
}

class SendGridEmailService implements EmailService {
  async send(to: string, subject: string, body: string): Promise<void> {
    // SendGrid-specific logic
  }
}

class BcryptHasher implements PasswordHasher {
  async hash(password: string): Promise<string> {
    return bcrypt.hash(password, 10);
  }
}
```

**Why this works**: Each component has a single responsibility. You can test UserService with mocks. Swapping implementations doesn't affect UserService.

### Anti-Pattern: Global State Coupling

```typescript
// Global configuration
const config = {
  apiUrl: 'https://api.example.com',
  timeout: 5000,
  retries: 3
};

class ApiClient {
  async fetch(endpoint: string) {
    // Directly uses global config
    const response = await fetch(`${config.apiUrl}${endpoint}`, {
      timeout: config.timeout
    });
    return response.json();
  }
}

class DataService {
  async getData() {
    const client = new ApiClient();
    return client.fetch('/data');
  }
}
```

**Why this fails**: Components are coupled to global state. Testing requires modifying globals. Multiple configurations aren't possible.

### Good Example: Explicit Dependencies

```typescript
interface ApiConfig {
  apiUrl: string;
  timeout: number;
  retries: number;
}

class ApiClient {
  constructor(private config: ApiConfig) {}

  async fetch(endpoint: string) {
    const response = await fetch(`${this.config.apiUrl}${endpoint}`, {
      timeout: this.config.timeout
    });
    return response.json();
  }
}

class DataService {
  constructor(private client: ApiClient) {}

  async getData() {
    return this.client.fetch('/data');
  }
}

// Usage
const config: ApiConfig = {
  apiUrl: 'https://api.example.com',
  timeout: 5000,
  retries: 3
};

const client = new ApiClient(config);
const service = new DataService(client);
```

**Why this works**: Dependencies are explicit. Easy to test with different configurations. Components are independent.

## Common Pitfalls

- **Shared mutable state**: Avoid global variables and singletons
- **Deep inheritance hierarchies**: Prefer composition over inheritance
- **God objects**: Classes that know or do too much
- **Hidden dependencies**: Components that reach out to globals or singletons

## Related Concepts

- [DRY Principle](../dry-principle/SKILL.md) - Avoid duplication
- [Design by Contract](../design-by-contract/SKILL.md) - Define clear interfaces

## References

- The Pragmatic Programmer (Hunt & Thomas)
- SOLID Principles (especially Single Responsibility and Dependency Inversion)

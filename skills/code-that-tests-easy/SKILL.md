---
name: code-that-tests-easy
description: |
  Design code for testability from the start. Testable code is better code.
  Use when architecting systems, writing functions, or refactoring for tests.
  Triggers: hard to test, complex setup, mocking difficulties, slow tests.
---

# Code That's Easy to Test

## Overview

If code is hard to test, it's poorly designed. Testability isn't an afterthought—it's a design principle. Code that's easy to test is loosely coupled, has clear dependencies, and follows single responsibility. Design for testability and you'll get better architecture as a side effect.

## When to Use

- Designing new components
- Refactoring existing code
- Tests require complex setup
- Mocking is difficult
- Tests are slow or flaky

## Core Principles

1. **Dependency Injection**: Pass dependencies explicitly
2. **Pure Functions**: Same input → same output
3. **Single Responsibility**: One reason to change
4. **Avoid Global State**: No hidden dependencies

## Examples

### Anti-Pattern: Hard to Test

```typescript
class UserService {
  async createUser(email: string, password: string) {
    // Hard-coded database connection
    const db = new Database('localhost', 'mydb');
    
    // Direct dependency on crypto
    const hash = crypto.createHash('sha256').update(password).digest('hex');
    
    // Hard-coded email service
    const emailer = new SendGridClient(process.env.SENDGRID_KEY);
    
    // Global state
    if (global.maintenanceMode) {
      throw new Error('Maintenance mode');
    }
    
    const user = await db.users.insert({ email, passwordHash: hash });
    await emailer.send(email, 'Welcome!');
    
    return user;
  }
}

// Testing requires:
// - Real database
// - Real SendGrid account
// - Mocking global state
// - Mocking crypto module
```

**Why this fails**: Can't test without external services. Slow tests. Brittle. Hard to mock dependencies.

### Good Example: Easy to Test

```typescript
interface Database {
  users: {
    insert(user: User): Promise<User>;
  };
}

interface EmailService {
  send(to: string, subject: string): Promise<void>;
}

interface PasswordHasher {
  hash(password: string): Promise<string>;
}

class UserService {
  constructor(
    private db: Database,
    private emailService: EmailService,
    private hasher: PasswordHasher,
    private maintenanceMode: boolean = false
  ) {}

  async createUser(email: string, password: string): Promise<User> {
    if (this.maintenanceMode) {
      throw new Error('Maintenance mode');
    }

    const passwordHash = await this.hasher.hash(password);
    const user = await this.db.users.insert({ email, passwordHash });
    await this.emailService.send(email, 'Welcome!');
    
    return user;
  }
}

// Testing is easy
describe('UserService', () => {
  it('creates user', async () => {
    const mockDb = {
      users: {
        insert: jest.fn().mockResolvedValue({ id: '1', email: 'test@example.com' })
      }
    };
    const mockEmail = { send: jest.fn() };
    const mockHasher = { hash: jest.fn().mockResolvedValue('hashed') };

    const service = new UserService(mockDb, mockEmail, mockHasher);
    const user = await service.createUser('test@example.com', 'password');

    expect(user.email).toBe('test@example.com');
    expect(mockHasher.hash).toHaveBeenCalledWith('password');
    expect(mockEmail.send).toHaveBeenCalled();
  });
});
```

**Why this works**: All dependencies injected. Easy to mock. Fast tests. No external services needed.

### Anti-Pattern: Impure Functions

```typescript
let requestCount = 0;

function processRequest(data: string): string {
  requestCount++;  // Side effect
  const timestamp = Date.now();  // Non-deterministic
  const random = Math.random();  // Non-deterministic
  
  return `${data}-${timestamp}-${random}-${requestCount}`;
}

// Impossible to test reliably
expect(processRequest('test')).toBe(???);  // Different every time
```

### Good Example: Pure Functions

```typescript
interface RequestContext {
  timestamp: number;
  random: number;
  requestCount: number;
}

function processRequest(data: string, context: RequestContext): string {
  return `${data}-${context.timestamp}-${context.random}-${context.requestCount}`;
}

// Easy to test
describe('processRequest', () => {
  it('formats request with context', () => {
    const context = { timestamp: 1234567890, random: 0.5, requestCount: 1 };
    expect(processRequest('test', context)).toBe('test-1234567890-0.5-1');
  });
});
```

### Anti-Pattern: Complex Setup

```typescript
class OrderProcessor {
  async process(orderId: string) {
    const order = await db.orders.findById(orderId);
    const user = await db.users.findById(order.userId);
    const items = await Promise.all(
      order.itemIds.map(id => db.items.findById(id))
    );
    const inventory = await Promise.all(
      items.map(item => db.inventory.findByItemId(item.id))
    );
    // ... 50 more lines
  }
}

// Test requires setting up:
// - Order in database
// - User in database
// - Multiple items in database
// - Inventory records
// - Complex data relationships
```

### Good Example: Simple Setup

```typescript
interface Order {
  id: string;
  userId: string;
  items: OrderItem[];
}

class OrderProcessor {
  constructor(
    private inventoryService: InventoryService,
    private paymentService: PaymentService
  ) {}

  async process(order: Order): Promise<ProcessedOrder> {
    await this.inventoryService.reserve(order.items);
    const payment = await this.paymentService.charge(order);
    
    return {
      orderId: order.id,
      status: 'completed',
      paymentId: payment.id
    };
  }
}

// Test is simple
describe('OrderProcessor', () => {
  it('processes order', async () => {
    const mockInventory = { reserve: jest.fn() };
    const mockPayment = { charge: jest.fn().mockResolvedValue({ id: 'pay123' }) };
    
    const processor = new OrderProcessor(mockInventory, mockPayment);
    const order = { id: 'ord1', userId: 'user1', items: [] };
    
    const result = await processor.process(order);
    
    expect(result.status).toBe('completed');
  });
});
```

## Common Pitfalls

- **Testing implementation**: Test behavior, not internals
- **Over-mocking**: Too many mocks indicate poor design
- **Slow tests**: Usually means too many real dependencies
- **Flaky tests**: Often caused by hidden state or timing issues

## Related Concepts

- [Test-Driven Development](../test-driven-development/SKILL.md) - Write tests first
- [Orthogonality](../orthogonality/SKILL.md) - Independent components

## References

- The Pragmatic Programmer (Hunt & Thomas)
- Growing Object-Oriented Software, Guided by Tests (Freeman & Pryce)

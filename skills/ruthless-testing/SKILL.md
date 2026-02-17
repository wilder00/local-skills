---
name: ruthless-testing
description: |
  Test thoroughly and systematically. Find bugs before your users do.
  Use when building production systems, before releases, or fixing bugs.
  Triggers: production deployment, bug reports, critical features, quality assurance.
---

# Ruthless Testing

## Overview

Testing isn't optional. Test early, test often, and test ruthlessly. Unit tests, integration tests, end-to-end tests—use them all. Automate everything. Find bugs before they reach production. Your users shouldn't be your QA team.

## When to Use

- Before every deployment
- After every feature
- When fixing bugs
- During refactoring
- Continuously in CI/CD

## Core Principles

1. **Test Pyramid**: Many unit tests, fewer integration tests, few E2E tests
2. **Automate Everything**: Manual testing doesn't scale
3. **Test Behavior**: Not implementation details
4. **Fast Feedback**: Tests should run quickly

## Test Types

### Unit Tests

```typescript
// Test individual functions in isolation
describe('calculateTax', () => {
  it('calculates 10% tax', () => {
    expect(calculateTax(100, 0.10)).toBe(10);
  });

  it('handles zero tax rate', () => {
    expect(calculateTax(100, 0)).toBe(0);
  });

  it('rounds to 2 decimal places', () => {
    expect(calculateTax(100, 0.075)).toBe(7.50);
  });
});
```

### Integration Tests

```typescript
// Test components working together
describe('UserService integration', () => {
  let db: Database;
  let service: UserService;

  beforeEach(async () => {
    db = await createTestDatabase();
    service = new UserService(db);
  });

  afterEach(async () => {
    await db.close();
  });

  it('creates and retrieves user', async () => {
    const created = await service.createUser('test@example.com', 'password');
    const retrieved = await service.getUserById(created.id);
    
    expect(retrieved).toEqual(created);
  });
});
```

### End-to-End Tests

```typescript
// Test complete user flows
describe('User registration flow', () => {
  it('allows new user to register and login', async () => {
    // Navigate to registration page
    await page.goto('http://localhost:3000/register');
    
    // Fill form
    await page.fill('[name="email"]', 'test@example.com');
    await page.fill('[name="password"]', 'SecurePass123');
    await page.click('button[type="submit"]');
    
    // Verify redirect to dashboard
    await expect(page).toHaveURL('http://localhost:3000/dashboard');
    await expect(page.locator('h1')).toContainText('Welcome');
  });
});
```

### Property-Based Testing

```typescript
import fc from 'fast-check';

describe('calculateDiscount', () => {
  it('never produces negative results', () => {
    fc.assert(
      fc.property(
        fc.float({ min: 0, max: 10000 }),
        fc.float({ min: 0, max: 100 }),
        (price, discount) => {
          const result = calculateDiscount(price, discount);
          return result >= 0;
        }
      )
    );
  });

  it('discount never exceeds original price', () => {
    fc.assert(
      fc.property(
        fc.float({ min: 0, max: 10000 }),
        fc.float({ min: 0, max: 100 }),
        (price, discount) => {
          const result = calculateDiscount(price, discount);
          return result <= price;
        }
      )
    );
  });
});
```

### Mutation Testing

```typescript
// Original code
function isEven(n: number): boolean {
  return n % 2 === 0;
}

// Mutation: Change === to !==
function isEven(n: number): boolean {
  return n % 2 !== 0;  // Mutant
}

// If tests still pass, they're insufficient
// Good tests will catch this mutation
describe('isEven', () => {
  it('returns true for even numbers', () => {
    expect(isEven(2)).toBe(true);
    expect(isEven(4)).toBe(true);
  });

  it('returns false for odd numbers', () => {
    expect(isEven(1)).toBe(false);
    expect(isEven(3)).toBe(false);
  });
});
```

### Test Coverage

```typescript
// Aim for high coverage, but 100% isn't always necessary
// Focus on critical paths and edge cases

// Critical: Payment processing
describe('PaymentProcessor', () => {
  it('processes valid payment', async () => { /* ... */ });
  it('rejects invalid card', async () => { /* ... */ });
  it('handles network timeout', async () => { /* ... */ });
  it('retries on temporary failure', async () => { /* ... */ });
  it('prevents double charging', async () => { /* ... */ });
});

// Less critical: UI formatting
describe('formatDate', () => {
  it('formats date correctly', () => { /* ... */ });
  // Don't need to test every edge case
});
```

### Test Organization

```typescript
// Arrange-Act-Assert pattern
describe('ShoppingCart', () => {
  it('calculates total with discount', () => {
    // Arrange
    const cart = new ShoppingCart();
    cart.addItem({ price: 100, quantity: 2 });
    cart.addItem({ price: 50, quantity: 1 });
    
    // Act
    const total = cart.calculateTotal(0.10);  // 10% discount
    
    // Assert
    expect(total).toBe(225);  // (200 + 50) * 0.9
  });
});
```

### Continuous Testing

```typescript
// package.json
{
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage",
    "test:e2e": "playwright test"
  }
}

// .github/workflows/test.yml
name: Tests
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - run: npm install
      - run: npm test
      - run: npm run test:e2e
```

## Common Pitfalls

- **Testing implementation**: Test behavior, not internals
- **Brittle tests**: Tests break on refactoring
- **Slow tests**: Optimize or parallelize
- **Ignoring flaky tests**: Fix or remove them
- **Low coverage**: Aim for 80%+ on critical code

## Related Concepts

- [Test-Driven Development](../test-driven-development/SKILL.md) - Write tests first
- [Code That Tests Easy](../code-that-tests-easy/SKILL.md) - Testable design

## References

- The Pragmatic Programmer (Hunt & Thomas)
- The Art of Software Testing (Glenford Myers)

---
name: test-driven-development
description: |
  Write tests before implementation to drive design and ensure correctness.
  Use when building new features, fixing bugs, or refactoring code.
  Triggers: new feature, bug fix, refactoring, unclear requirements, design decisions.
---

# Test-Driven Development

## Overview

Test-Driven Development (TDD) inverts the traditional development process: write the test first, watch it fail, then write just enough code to make it pass. This cycle—Red, Green, Refactor—drives better design, ensures test coverage, and provides immediate feedback.

## When to Use

- Building new features
- Fixing bugs
- Refactoring existing code
- Clarifying requirements
- Designing APIs

## Core Principles

1. **Red**: Write a failing test
2. **Green**: Write minimal code to pass
3. **Refactor**: Improve code while keeping tests green
4. **Small Steps**: One test at a time

## Examples

### The TDD Cycle

```typescript
// Step 1: RED - Write failing test
describe('calculateDiscount', () => {
  it('applies percentage discount to price', () => {
    expect(calculateDiscount(100, 10)).toBe(10);
  });
});

// Test fails: calculateDiscount is not defined

// Step 2: GREEN - Minimal implementation
function calculateDiscount(price: number, percent: number): number {
  return price * (percent / 100);
}

// Test passes

// Step 3: Add more tests
describe('calculateDiscount', () => {
  it('applies percentage discount to price', () => {
    expect(calculateDiscount(100, 10)).toBe(10);
  });

  it('handles zero discount', () => {
    expect(calculateDiscount(100, 0)).toBe(0);
  });

  it('handles 100% discount', () => {
    expect(calculateDiscount(100, 100)).toBe(100);
  });

  it('throws on negative price', () => {
    expect(() => calculateDiscount(-100, 10)).toThrow('Price cannot be negative');
  });
});

// Step 4: REFACTOR - Add validation
function calculateDiscount(price: number, percent: number): number {
  if (price < 0) {
    throw new Error('Price cannot be negative');
  }
  if (percent < 0 || percent > 100) {
    throw new Error('Discount must be between 0 and 100');
  }
  return price * (percent / 100);
}

// All tests still pass
```

### TDD for Bug Fixes

```typescript
// Bug report: User registration fails for emails with + character

// Step 1: Write test that reproduces the bug
describe('UserService', () => {
  it('registers user with + in email', async () => {
    const email = 'user+test@example.com';
    const user = await userService.register(email, 'password123');
    expect(user.email).toBe(email);
  });
});

// Test fails (reproduces bug)

// Step 2: Fix the bug
class UserService {
  async register(email: string, password: string): Promise<User> {
    // Fixed: was using wrong regex that rejected +
    if (!email.match(/^[^\s@]+@[^\s@]+\.[^\s@]+$/)) {
      throw new ValidationError('Invalid email');
    }
    // ... rest of implementation
  }
}

// Test passes (bug fixed)
```

### TDD for API Design

```typescript
// Design a shopping cart API using TDD

// Test 1: Create empty cart
describe('ShoppingCart', () => {
  it('starts empty', () => {
    const cart = new ShoppingCart();
    expect(cart.itemCount).toBe(0);
    expect(cart.total).toBe(0);
  });
});

// Implement
class ShoppingCart {
  private items: CartItem[] = [];

  get itemCount(): number {
    return this.items.length;
  }

  get total(): number {
    return 0;
  }
}

// Test 2: Add item
it('adds item to cart', () => {
  const cart = new ShoppingCart();
  cart.addItem({ id: '1', name: 'Widget', price: 10 });
  expect(cart.itemCount).toBe(1);
  expect(cart.total).toBe(10);
});

// Implement
class ShoppingCart {
  private items: CartItem[] = [];

  addItem(item: CartItem): void {
    this.items.push(item);
  }

  get itemCount(): number {
    return this.items.length;
  }

  get total(): number {
    return this.items.reduce((sum, item) => sum + item.price, 0);
  }
}

// Continue with more tests...
```

### TDD Benefits

```typescript
// Without TDD: Unclear requirements
function processPayment(amount, userId, method) {
  // What should happen if amount is negative?
  // What if userId doesn't exist?
  // What payment methods are supported?
  // Implementation is guesswork
}

// With TDD: Requirements emerge from tests
describe('processPayment', () => {
  it('processes credit card payment', async () => {
    const result = await processPayment(100, 'user1', 'credit_card');
    expect(result.status).toBe('success');
  });

  it('rejects negative amounts', async () => {
    await expect(processPayment(-100, 'user1', 'credit_card'))
      .rejects.toThrow('Amount must be positive');
  });

  it('rejects invalid user', async () => {
    await expect(processPayment(100, 'invalid', 'credit_card'))
      .rejects.toThrow('User not found');
  });

  it('rejects unsupported payment method', async () => {
    await expect(processPayment(100, 'user1', 'bitcoin'))
      .rejects.toThrow('Unsupported payment method');
  });
});

// Tests clarify requirements before implementation
```

## Common Pitfalls

- **Writing tests after code**: Defeats the purpose
- **Testing implementation details**: Test behavior, not internals
- **Large test steps**: Keep tests small and focused
- **Skipping refactor**: Green isn't enough, code must be clean

## Related Concepts

- [Code That Tests Easy](../code-that-tests-easy/SKILL.md) - Testable design
- [Refactoring Early](../refactoring-early/SKILL.md) - Continuous improvement

## References

- The Pragmatic Programmer (Hunt & Thomas)
- Test Driven Development: By Example (Kent Beck)

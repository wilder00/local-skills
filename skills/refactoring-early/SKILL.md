---
name: refactoring-early
description: |
  Refactor continuously as you learn, don't wait for code to rot.
  Use when code smells emerge, requirements change, or understanding improves.
  Triggers: duplication, complexity, unclear names, long functions, code review feedback.
---

# Refactoring Early

## Overview

Code rots. Requirements change, understanding deepens, and yesterday's good design becomes today's technical debt. Refactor early and often—don't wait for the perfect moment. Small, continuous improvements prevent the need for large, risky rewrites.

## When to Use

- Code duplication appears
- Functions grow too long
- Names become unclear
- Complexity increases
- Tests become difficult
- After gaining new understanding

## Core Principles

1. **Continuous Improvement**: Refactor as part of normal development
2. **Small Steps**: Make incremental changes, not big rewrites
3. **Test Coverage**: Refactor with confidence using tests
4. **Boy Scout Rule**: Leave code better than you found it

## Examples

### Anti-Pattern: Letting Code Rot

```typescript
// Original function (6 months ago)
function processOrder(order) {
  // ... 50 lines of code
}

// After many features added (today)
function processOrder(order, discount, coupon, giftWrap, expressShipping, insurance, customMessage) {
  // ... 300 lines of tangled logic
  // Multiple responsibilities
  // Hard to test
  // Impossible to understand
}
```

### Good Example: Continuous Refactoring

```typescript
// Week 1: Simple function
function processOrder(order: Order): ProcessedOrder {
  return {
    id: order.id,
    total: calculateTotal(order.items)
  };
}

// Week 3: Extract discount logic when added
function processOrder(order: Order, discount?: Discount): ProcessedOrder {
  const subtotal = calculateTotal(order.items);
  const total = applyDiscount(subtotal, discount);
  return { id: order.id, total };
}

function applyDiscount(amount: number, discount?: Discount): number {
  if (!discount) return amount;
  return amount * (1 - discount.percentage / 100);
}

// Week 5: Extract to service when complexity grows
class OrderProcessor {
  constructor(
    private discountService: DiscountService,
    private shippingService: ShippingService
  ) {}

  process(order: Order, options: OrderOptions): ProcessedOrder {
    const subtotal = this.calculateSubtotal(order.items);
    const discount = this.discountService.calculate(order, options.discount);
    const shipping = this.shippingService.calculate(order, options.shipping);
    
    return {
      id: order.id,
      subtotal,
      discount,
      shipping,
      total: subtotal - discount + shipping
    };
  }

  private calculateSubtotal(items: OrderItem[]): number {
    return items.reduce((sum, item) => sum + item.price * item.quantity, 0);
  }
}
```

### Refactoring Techniques

```typescript
// Before: Long function
function createUser(email, password, firstName, lastName, phone, address) {
  if (!email.includes('@')) throw new Error('Invalid email');
  if (password.length < 8) throw new Error('Password too short');
  const hash = crypto.createHash('sha256').update(password).digest('hex');
  const user = { email, passwordHash: hash, firstName, lastName, phone, address };
  db.users.insert(user);
  sendEmail(email, 'Welcome!', 'Thanks for signing up');
  return user;
}

// After: Extract methods
function createUser(userData: UserData): User {
  validateUserData(userData);
  const user = buildUser(userData);
  saveUser(user);
  sendWelcomeEmail(user.email);
  return user;
}

function validateUserData(data: UserData): void {
  validateEmail(data.email);
  validatePassword(data.password);
}

function buildUser(data: UserData): User {
  return {
    email: data.email,
    passwordHash: hashPassword(data.password),
    firstName: data.firstName,
    lastName: data.lastName,
    phone: data.phone,
    address: data.address
  };
}
```

## Common Pitfalls

- **Refactoring without tests**: Always have test coverage first
- **Big bang refactoring**: Make small, incremental changes
- **Refactoring and adding features**: Do one or the other, not both
- **Perfectionism**: Good enough is better than perfect

## Related Concepts

- [DRY Principle](../dry-principle/SKILL.md) - Eliminate duplication
- [Code That Tests Easy](../code-that-tests-easy/SKILL.md) - Testable design

## References

- The Pragmatic Programmer (Hunt & Thomas)
- Refactoring (Martin Fowler)

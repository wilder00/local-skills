---
name: design-by-contract
description: |
  Define clear contracts with preconditions, postconditions, and invariants.
  Use when designing APIs, functions, or class interfaces to establish expectations.
  Triggers: unclear requirements, API design, validation, error handling, documentation.
---

# Design by Contract

## Overview

Every function and method has a contract with its callers: "If you promise to call me with valid inputs (preconditions), I promise to deliver valid outputs (postconditions) and maintain system consistency (invariants)." Design by Contract makes these promises explicit, turning assumptions into verifiable guarantees.

## When to Use

- Designing public APIs
- Writing library functions
- Defining class interfaces
- Establishing system boundaries
- Documenting expectations

## Core Principles

1. **Preconditions**: What must be true before the function executes
2. **Postconditions**: What the function guarantees upon completion
3. **Invariants**: What remains true throughout execution
4. **Clear Responsibility**: Caller ensures preconditions, function ensures postconditions

## Implementation Guidelines

### Define Preconditions

- Document input requirements
- Validate at function entry
- Fail fast if violated
- Make expectations explicit

### Establish Postconditions

- Document output guarantees
- Ensure promises are kept
- Handle all edge cases
- Return consistent results

### Maintain Invariants

- Define class invariants
- Preserve them across operations
- Validate in critical sections
- Document assumptions

## Examples

### Anti-Pattern: Unclear Contract

```typescript
// What does this function expect? What does it guarantee?
function processPayment(amount: number, userId: string) {
  const user = users.find(u => u.id === userId);
  const balance = user.balance - amount;
  user.balance = balance;
  return balance;
}

// Usage leads to bugs
processPayment(-100, 'user123');  // Negative amount?
processPayment(50, 'invalid');     // Invalid user?
processPayment(1000000, 'user1');  // Insufficient funds?
```

**Why this fails**: No clear contract. Undefined behavior for invalid inputs. Caller doesn't know what to expect. Function doesn't validate assumptions.

### Good Example: Explicit Contract

```typescript
/**
 * Processes a payment by deducting amount from user's balance.
 * 
 * Preconditions:
 * - amount must be positive
 * - userId must reference an existing user
 * - user must have sufficient balance
 * 
 * Postconditions:
 * - user's balance is reduced by amount
 * - returns new balance
 * - transaction is logged
 * 
 * @throws {ValidationError} if amount <= 0
 * @throws {NotFoundError} if user doesn't exist
 * @throws {InsufficientFundsError} if balance < amount
 */
function processPayment(amount: number, userId: string): number {
  // Validate preconditions
  if (amount <= 0) {
    throw new ValidationError('Amount must be positive');
  }

  const user = users.find(u => u.id === userId);
  if (!user) {
    throw new NotFoundError(`User ${userId} not found`);
  }

  if (user.balance < amount) {
    throw new InsufficientFundsError(
      `Insufficient funds: ${user.balance} < ${amount}`
    );
  }

  // Execute operation
  user.balance -= amount;
  logTransaction(userId, amount);

  // Postcondition guaranteed
  return user.balance;
}

// Usage is clear and safe
try {
  const newBalance = processPayment(50, 'user123');
  console.log(`Payment processed. New balance: ${newBalance}`);
} catch (error) {
  if (error instanceof ValidationError) {
    console.error('Invalid payment amount');
  } else if (error instanceof InsufficientFundsError) {
    console.error('Not enough funds');
  }
}
```

**Why this works**: Contract is explicit and documented. Preconditions are validated. Postconditions are guaranteed. Caller knows exactly what to expect.

### Anti-Pattern: Weak Invariants

```typescript
class BankAccount {
  balance: number = 0;

  deposit(amount: number) {
    this.balance += amount;
  }

  withdraw(amount: number) {
    this.balance -= amount;
  }
}

// Invariant violated
const account = new BankAccount();
account.deposit(-100);      // Negative deposit?
account.withdraw(1000);     // Negative balance?
account.balance = -500;     // Direct manipulation?
```

**Why this fails**: No invariants enforced. Balance can become negative. Invalid operations allowed. State can be corrupted.

### Good Example: Strong Invariants

```typescript
class BankAccount {
  private _balance: number = 0;

  /**
   * Invariant: balance >= 0 at all times
   */
  get balance(): number {
    return this._balance;
  }

  /**
   * Deposits amount into account.
   * 
   * Precondition: amount > 0
   * Postcondition: balance increases by amount
   * Invariant: balance >= 0
   */
  deposit(amount: number): void {
    if (amount <= 0) {
      throw new ValidationError('Deposit amount must be positive');
    }

    this._balance += amount;
    this.assertInvariant();
  }

  /**
   * Withdraws amount from account.
   * 
   * Precondition: amount > 0 and amount <= balance
   * Postcondition: balance decreases by amount
   * Invariant: balance >= 0
   */
  withdraw(amount: number): void {
    if (amount <= 0) {
      throw new ValidationError('Withdrawal amount must be positive');
    }

    if (amount > this._balance) {
      throw new InsufficientFundsError(
        `Cannot withdraw ${amount}, balance is ${this._balance}`
      );
    }

    this._balance -= amount;
    this.assertInvariant();
  }

  private assertInvariant(): void {
    if (this._balance < 0) {
      throw new InvariantViolationError('Balance cannot be negative');
    }
  }
}

// Invariant is protected
const account = new BankAccount();
account.deposit(100);           // ✓ Valid
account.withdraw(50);           // ✓ Valid
account.withdraw(100);          // ✗ Throws InsufficientFundsError
account.deposit(-50);           // ✗ Throws ValidationError
// account.balance = -100;      // ✗ Compile error (private)
```

**Why this works**: Invariant is clearly defined and enforced. Invalid operations are prevented. State corruption is impossible. Contract is maintained.

### TypeScript Type-Level Contracts

```typescript
// Use types to encode contracts
type PositiveNumber = number & { __brand: 'positive' };
type UserId = string & { __brand: 'userId' };

function createPositiveNumber(n: number): PositiveNumber {
  if (n <= 0) throw new ValidationError('Must be positive');
  return n as PositiveNumber;
}

function createUserId(id: string): UserId {
  if (!id.match(/^user-[0-9]+$/)) {
    throw new ValidationError('Invalid user ID format');
  }
  return id as UserId;
}

// Contract enforced at type level
function processPayment(amount: PositiveNumber, userId: UserId): number {
  // Preconditions guaranteed by types
  const user = users.find(u => u.id === userId);
  // ... rest of implementation
}

// Usage
const amount = createPositiveNumber(50);  // Validates
const userId = createUserId('user-123'); // Validates
processPayment(amount, userId);           // Type-safe

// processPayment(50, 'user-123');        // ✗ Type error
// processPayment(-50, userId);           // ✗ Caught at creation
```

**Why this works**: Types encode preconditions. Invalid calls caught at compile time. Runtime validation at boundaries. Self-documenting code.

## Common Pitfalls

- **Defensive programming everywhere**: Don't validate in both caller and callee
- **Weak error messages**: Explain what was expected and what was received
- **Ignoring postconditions**: Don't just validate inputs, guarantee outputs
- **Mutable contracts**: Contracts should be stable and well-documented

## Related Concepts

- [Defensive Programming](../defensive-programming/SKILL.md) - Assertive validation
- [Orthogonality](../orthogonality/SKILL.md) - Clear interfaces

## References

- The Pragmatic Programmer (Hunt & Thomas)
- Design by Contract (Bertrand Meyer)
- Eiffel programming language

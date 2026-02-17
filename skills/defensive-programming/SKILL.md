---
name: defensive-programming
description: |
  Write assertive code that validates assumptions and fails fast on errors.
  Use when handling external input, integrating systems, or dealing with uncertainty.
  Triggers: error handling, validation, null checks, edge cases, robustness.
---

# Defensive Programming

## Overview

You can't write perfect code. Systems fail, users provide bad input, and assumptions prove wrong. Defensive programming means coding as if you don't trust anything—including yourself. Assert your assumptions, validate inputs, and fail fast when something goes wrong.

## When to Use

- Handling user input or external data
- Integrating with third-party systems
- Working with network requests
- Processing file uploads
- Dealing with configuration

## Core Principles

1. **Assert Assumptions**: Make implicit assumptions explicit
2. **Validate Everything**: Don't trust any external input
3. **Fail Fast**: Detect problems immediately
4. **Provide Context**: Error messages should aid debugging

## Examples

### Anti-Pattern: Trusting Input

```typescript
function calculateDiscount(price: number, discountPercent: number) {
  return price * (discountPercent / 100);
}

// Crashes or produces nonsense
calculateDiscount(undefined, 20);  // NaN
calculateDiscount(100, -50);       // Negative discount
calculateDiscount(100, 150);       // More than 100% off
```

### Good Example: Defensive Validation

```typescript
function calculateDiscount(price: number, discountPercent: number): number {
  if (typeof price !== 'number' || isNaN(price)) {
    throw new ValidationError('Price must be a valid number');
  }
  if (price < 0) {
    throw new ValidationError('Price cannot be negative');
  }
  if (typeof discountPercent !== 'number' || isNaN(discountPercent)) {
    throw new ValidationError('Discount must be a valid number');
  }
  if (discountPercent < 0 || discountPercent > 100) {
    throw new ValidationError('Discount must be between 0 and 100');
  }

  return price * (discountPercent / 100);
}
```

### Anti-Pattern: Silent Failures

```typescript
async function fetchUserData(userId: string) {
  try {
    const response = await fetch(`/api/users/${userId}`);
    return response.json();
  } catch (error) {
    return null;  // Swallows error
  }
}
```

### Good Example: Explicit Error Handling

```typescript
async function fetchUserData(userId: string): Promise<User> {
  if (!userId) {
    throw new ValidationError('User ID is required');
  }

  try {
    const response = await fetch(`/api/users/${userId}`);
    
    if (!response.ok) {
      throw new ApiError(
        `Failed to fetch user: ${response.status} ${response.statusText}`,
        response.status
      );
    }

    const data = await response.json();
    
    if (!isValidUser(data)) {
      throw new DataError('Invalid user data received from API');
    }

    return data;
  } catch (error) {
    if (error instanceof ApiError || error instanceof DataError) {
      throw error;
    }
    throw new NetworkError(`Network error fetching user ${userId}`, error);
  }
}
```

### Use Assertions for Invariants

```typescript
function processQueue<T>(queue: T[]): T | null {
  // Assert precondition
  console.assert(Array.isArray(queue), 'Queue must be an array');
  
  if (queue.length === 0) {
    return null;
  }

  const item = queue.shift();
  
  // Assert postcondition
  console.assert(item !== undefined, 'Shift should return item when queue not empty');
  
  return item!;
}
```

## Common Pitfalls

- **Over-defensive**: Don't validate internal function calls
- **Poor error messages**: Include context and expected values
- **Catching everything**: Let unexpected errors bubble up
- **Defensive in wrong places**: Focus on boundaries

## Related Concepts

- [Design by Contract](../design-by-contract/SKILL.md) - Explicit contracts
- [Pragmatic Paranoia](../pragmatic-paranoia/SKILL.md) - Defensive mindset

## References

- The Pragmatic Programmer (Hunt & Thomas)

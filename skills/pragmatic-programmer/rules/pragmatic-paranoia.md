---
title: Pragmatic Paranoia
impact: HIGH
tags: error-handling, resilience, fault-tolerance, defensive
---


## Pragmatic Paranoia

## Overview

Perfect software doesn't exist. Networks fail, disks fill up, users provide bad input, and bugs hide in plain sight. Pragmatic paranoia means coding defensively—assume things will go wrong and handle failures gracefully. It's not pessimism; it's realism.

## When to Use

- Building production systems
- Handling external dependencies
- Processing user input
- Dealing with network requests
- Managing resources

## Core Principles

1. **Assume Failure**: Things will go wrong
2. **Fail Gracefully**: Handle errors without crashing
3. **Provide Fallbacks**: Have plan B (and C)
4. **Monitor and Alert**: Know when things break

## Examples

### Anti-Pattern: Optimistic Code

```typescript
async function getUserProfile(userId: string) {
  const user = await db.users.findById(userId);
  const posts = await db.posts.findByUserId(userId);
  const followers = await db.followers.countByUserId(userId);
  
  return {
    name: user.name,
    email: user.email,
    postCount: posts.length,
    followerCount: followers
  };
}
```

**Why this fails**: No error handling. Assumes database is always available. Assumes user exists. One failure crashes everything.

### Good Example: Paranoid Code

```typescript
async function getUserProfile(userId: string): Promise<UserProfile> {
  try {
    const user = await db.users.findById(userId);
    if (!user) {
      throw new NotFoundError(`User ${userId} not found`);
    }

    // Fetch additional data with fallbacks
    const [posts, followerCount] = await Promise.allSettled([
      db.posts.findByUserId(userId),
      db.followers.countByUserId(userId)
    ]);

    return {
      name: user.name,
      email: user.email,
      postCount: posts.status === 'fulfilled' ? posts.value.length : 0,
      followerCount: followerCount.status === 'fulfilled' ? followerCount.value : 0
    };
  } catch (error) {
    if (error instanceof NotFoundError) {
      throw error;
    }
    
    logger.error('Failed to fetch user profile', { userId, error });
    throw new ServiceError('Unable to fetch user profile', error);
  }
}
```

### Circuit Breaker Pattern

```typescript
class CircuitBreaker {
  private failures = 0;
  private lastFailureTime?: number;
  private state: 'closed' | 'open' | 'half-open' = 'closed';

  constructor(
    private threshold: number = 5,
    private timeout: number = 60000
  ) {}

  async execute<T>(fn: () => Promise<T>): Promise<T> {
    if (this.state === 'open') {
      if (Date.now() - this.lastFailureTime! > this.timeout) {
        this.state = 'half-open';
      } else {
        throw new CircuitOpenError('Circuit breaker is open');
      }
    }

    try {
      const result = await fn();
      this.onSuccess();
      return result;
    } catch (error) {
      this.onFailure();
      throw error;
    }
  }

  private onSuccess(): void {
    this.failures = 0;
    this.state = 'closed';
  }

  private onFailure(): void {
    this.failures++;
    this.lastFailureTime = Date.now();
    
    if (this.failures >= this.threshold) {
      this.state = 'open';
      logger.warn('Circuit breaker opened', { failures: this.failures });
    }
  }
}

// Usage
const apiBreaker = new CircuitBreaker();

async function callExternalApi() {
  return apiBreaker.execute(() => fetch('https://api.example.com/data'));
}
```

### Retry with Exponential Backoff

```typescript
async function retryWithBackoff<T>(
  fn: () => Promise<T>,
  maxRetries: number = 3,
  baseDelay: number = 1000
): Promise<T> {
  for (let attempt = 0; attempt <= maxRetries; attempt++) {
    try {
      return await fn();
    } catch (error) {
      if (attempt === maxRetries) {
        throw error;
      }

      const delay = baseDelay * Math.pow(2, attempt);
      logger.warn(`Attempt ${attempt + 1} failed, retrying in ${delay}ms`, { error });
      await new Promise(resolve => setTimeout(resolve, delay));
    }
  }

  throw new Error('Unreachable');
}

// Usage
const data = await retryWithBackoff(() => fetchFromUnreliableApi());
```

## Common Pitfalls

- **Swallowing errors**: Log and handle, don't ignore
- **Over-engineering**: Balance paranoia with pragmatism
- **No monitoring**: If you can't see failures, you can't fix them
- **Ignoring edge cases**: Test failure scenarios

## Related Concepts

- [Defensive Programming](../defensive-programming/SKILL.md) - Assertive validation
- [Design by Contract](../design-by-contract/SKILL.md) - Clear contracts

## References

- The Pragmatic Programmer (Hunt & Thomas)
- Release It! (Michael Nygard)

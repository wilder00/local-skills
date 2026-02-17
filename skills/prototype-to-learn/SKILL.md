---
name: prototype-to-learn
description: |
  Build throwaway prototypes to explore ideas and reduce uncertainty.
  Use when facing unknowns, evaluating technologies, or validating concepts.
  Triggers: new technology, unclear requirements, proof of concept, experimentation.
---

# Prototype to Learn

## Overview

Prototypes are disposable code written to answer questions and reduce risk. Unlike tracer bullets (which become production code), prototypes are meant to be thrown away. Build them to learn, then build the real thing properly with that knowledge.

## When to Use

- Evaluating new technologies or frameworks
- Exploring UI/UX concepts
- Validating performance assumptions
- Understanding third-party APIs
- Proving feasibility of an approach

## Core Principles

1. **Disposable**: Prototypes are throwaway code
2. **Focused**: Answer specific questions
3. **Quick and Dirty**: Ignore quality for speed
4. **Learning Tool**: Goal is knowledge, not production code

## Examples

### Anti-Pattern: Prototype Becomes Production

```typescript
// Quick prototype to test API integration
const express = require('express');
const app = express();

app.get('/data', (req, res) => {
  // No error handling
  // No validation
  // Hardcoded credentials
  fetch('https://api.example.com/data?key=abc123')
    .then(r => r.json())
    .then(data => res.json(data));
});

app.listen(3000);

// 6 months later: Still in production
// No tests, no error handling, security issues
```

**Why this fails**: Prototype quality code in production. Technical debt accumulates. Refactoring becomes harder over time.

### Good Example: Prototype Then Rebuild

```typescript
// Phase 1: Prototype (1 day)
// Goal: Can we integrate with this API?
async function testApiIntegration() {
  const response = await fetch('https://api.example.com/data?key=test');
  const data = await response.json();
  console.log('API works!', data);
  // Learned: API returns nested structure, rate limited, requires OAuth
}

// Phase 2: Production implementation (3 days)
interface ApiConfig {
  baseUrl: string;
  clientId: string;
  clientSecret: string;
}

class ExternalApiClient {
  private accessToken?: string;
  
  constructor(private config: ApiConfig) {}

  async getData(): Promise<ApiData> {
    await this.ensureAuthenticated();
    
    const response = await fetch(`${this.config.baseUrl}/data`, {
      headers: {
        'Authorization': `Bearer ${this.accessToken}`,
        'Content-Type': 'application/json'
      }
    });

    if (!response.ok) {
      throw new ApiError(`API request failed: ${response.status}`);
    }

    return this.parseResponse(await response.json());
  }

  private async ensureAuthenticated(): Promise<void> {
    if (this.accessToken) return;
    // OAuth flow implementation
  }

  private parseResponse(raw: unknown): ApiData {
    // Proper parsing and validation
  }
}
```

**Why this works**: Prototype answered questions quickly. Production code built with proper design. Knowledge from prototype informed implementation.

### Prototype for UI Exploration

```typescript
// Prototype: Test different layouts (HTML/CSS only)
// Try 3-4 different approaches in a few hours
// Get feedback from users
// Throw away the code

// Then build production version with proper React components
interface DashboardProps {
  data: DashboardData;
  onRefresh: () => void;
}

export function Dashboard({ data, onRefresh }: DashboardProps) {
  // Proper implementation based on prototype learnings
  return (
    <div className="dashboard">
      <Header onRefresh={onRefresh} />
      <MetricsGrid metrics={data.metrics} />
      <ChartSection charts={data.charts} />
    </div>
  );
}
```

### Prototype for Performance Testing

```typescript
// Prototype: Which approach is faster?
console.time('approach1');
for (let i = 0; i < 1000000; i++) {
  // Test approach 1
}
console.timeEnd('approach1');

console.time('approach2');
for (let i = 0; i < 1000000; i++) {
  // Test approach 2
}
console.timeEnd('approach2');

// Result: Approach 2 is 3x faster
// Now implement approach 2 properly in production
```

## Common Pitfalls

- **Prototype becomes production**: Always rebuild properly
- **Over-engineering prototypes**: Keep them quick and dirty
- **Prototyping everything**: Use for unknowns, not routine work
- **Ignoring lessons learned**: Document insights from prototypes

## Related Concepts

- [Tracer Bullets](../tracer-bullets/SKILL.md) - Production-quality skeleton
- [Reversibility](../reversibility/SKILL.md) - Keep options open

## References

- The Pragmatic Programmer (Hunt & Thomas)

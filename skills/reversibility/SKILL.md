---
name: reversibility
description: |
  Avoid irreversible architectural decisions by keeping options open.
  Use when making technology choices, designing systems, or planning architecture.
  Triggers: vendor lock-in, hard-coded dependencies, inflexible design, migration concerns.
---

# Reversibility

## Overview

There are no final decisions. Requirements change, technologies evolve, and business needs shift. Design systems that allow you to change your mind without catastrophic rewrites. Reversibility means keeping your options open and avoiding decisions that are expensive or impossible to undo.

## When to Use

- Choosing databases, frameworks, or cloud providers
- Designing system architecture
- Selecting third-party services
- Making technology commitments
- Planning long-term projects

## Core Principles

1. **Abstraction Layers**: Hide implementation details behind interfaces
2. **Loose Coupling**: Minimize dependencies on specific technologies
3. **Flexibility**: Design for change, not just current requirements
4. **Exit Strategy**: Always have a plan B

## Implementation Guidelines

### Abstract External Dependencies

- Wrap third-party libraries in your own interfaces
- Use adapters for external services
- Avoid spreading vendor-specific code throughout your system
- Keep integration points isolated

### Design for Replaceability

- Use dependency injection
- Program to interfaces, not implementations
- Avoid tight coupling to frameworks
- Keep business logic independent of infrastructure

### Document Assumptions

- Record why decisions were made
- Track dependencies and their alternatives
- Maintain architecture decision records (ADRs)

## Examples

### Anti-Pattern: Vendor Lock-in

```typescript
// Directly using AWS SDK throughout the application
import { S3 } from 'aws-sdk';

class ImageService {
  private s3 = new S3();

  async uploadImage(key: string, data: Buffer) {
    await this.s3.putObject({
      Bucket: 'my-bucket',
      Key: key,
      Body: data,
      ACL: 'public-read'
    }).promise();
    
    return `https://my-bucket.s3.amazonaws.com/${key}`;
  }

  async getImage(key: string) {
    const result = await this.s3.getObject({
      Bucket: 'my-bucket',
      Key: key
    }).promise();
    
    return result.Body;
  }
}

// Used in 50+ places across the codebase
const imageService = new ImageService();
```

**Why this fails**: Switching from AWS S3 to Google Cloud Storage or Azure Blob requires rewriting ImageService and potentially all usage sites. AWS-specific concepts leak throughout the code.

### Good Example: Abstracted Storage

```typescript
// Storage abstraction
interface StorageService {
  upload(key: string, data: Buffer): Promise<string>;
  download(key: string): Promise<Buffer>;
  delete(key: string): Promise<void>;
}

// AWS implementation
class S3StorageService implements StorageService {
  private s3 = new S3();
  
  async upload(key: string, data: Buffer): Promise<string> {
    await this.s3.putObject({
      Bucket: process.env.S3_BUCKET,
      Key: key,
      Body: data
    }).promise();
    
    return `https://${process.env.S3_BUCKET}.s3.amazonaws.com/${key}`;
  }

  async download(key: string): Promise<Buffer> {
    const result = await this.s3.getObject({
      Bucket: process.env.S3_BUCKET,
      Key: key
    }).promise();
    
    return result.Body as Buffer;
  }

  async delete(key: string): Promise<void> {
    await this.s3.deleteObject({
      Bucket: process.env.S3_BUCKET,
      Key: key
    }).promise();
  }
}

// Alternative implementation (easy to swap)
class LocalStorageService implements StorageService {
  async upload(key: string, data: Buffer): Promise<string> {
    await fs.writeFile(`./uploads/${key}`, data);
    return `/uploads/${key}`;
  }

  async download(key: string): Promise<Buffer> {
    return fs.readFile(`./uploads/${key}`);
  }

  async delete(key: string): Promise<void> {
    await fs.unlink(`./uploads/${key}`);
  }
}

// Application code depends on interface, not implementation
class ImageService {
  constructor(private storage: StorageService) {}

  async uploadImage(key: string, data: Buffer) {
    return this.storage.upload(key, data);
  }
}

// Easy to switch implementations
const storage = process.env.NODE_ENV === 'production'
  ? new S3StorageService()
  : new LocalStorageService();

const imageService = new ImageService(storage);
```

**Why this works**: Switching storage providers requires only implementing the interface. Application code remains unchanged. Can use local storage for development and S3 for production.

### Anti-Pattern: Framework Coupling

```typescript
// Business logic tightly coupled to Express
import { Request, Response } from 'express';

class UserController {
  async createUser(req: Request, res: Response) {
    const { email, password } = req.body;
    
    // Validation mixed with framework
    if (!email) {
      return res.status(400).json({ error: 'Email required' });
    }
    
    // Business logic mixed with HTTP
    const user = await db.users.create({ email, password });
    
    // Response handling
    res.status(201).json(user);
  }
}
```

**Why this fails**: Switching to Fastify, Hono, or another framework requires rewriting all controllers. Business logic is inseparable from HTTP handling.

### Good Example: Framework-Independent Logic

```typescript
// Pure business logic
interface CreateUserInput {
  email: string;
  password: string;
}

class UserService {
  async createUser(input: CreateUserInput): Promise<User> {
    if (!input.email) {
      throw new ValidationError('Email required');
    }
    
    return db.users.create(input);
  }
}

// Thin adapter for Express
class ExpressUserController {
  constructor(private userService: UserService) {}

  createUser = async (req: Request, res: Response) => {
    try {
      const user = await this.userService.createUser(req.body);
      res.status(201).json(user);
    } catch (error) {
      if (error instanceof ValidationError) {
        res.status(400).json({ error: error.message });
      } else {
        res.status(500).json({ error: 'Internal error' });
      }
    }
  };
}

// Easy to add adapter for different framework
class FastifyUserController {
  constructor(private userService: UserService) {}

  createUser = async (request: FastifyRequest, reply: FastifyReply) => {
    try {
      const user = await this.userService.createUser(request.body);
      reply.code(201).send(user);
    } catch (error) {
      // Fastify-specific error handling
    }
  };
}
```

**Why this works**: Business logic is framework-independent. Switching frameworks requires only new adapters. Core logic remains untouched.

## Common Pitfalls

- **Premature optimization**: Don't abstract everything "just in case"
- **Over-engineering**: Balance flexibility with simplicity
- **Ignoring costs**: Abstraction has overhead—use when benefits outweigh costs
- **Analysis paralysis**: Make decisions, but keep them reversible

## Related Concepts

- [Orthogonality](../orthogonality/SKILL.md) - Independent components
- [Tracer Bullets](../tracer-bullets/SKILL.md) - Iterative development

## References

- The Pragmatic Programmer (Hunt & Thomas)
- Hexagonal Architecture (Ports and Adapters)
- Clean Architecture (Robert C. Martin)

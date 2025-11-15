---
name: system-design
description: Expert in system architecture and design patterns. Use for designing scalable systems, microservices, distributed systems, and solution architecture.
allowed-tools: Read, Write, Edit, Grep, Glob, Bash
---

# System Design & Solution Architecture Expert

## Purpose
Provide expert guidance on system architecture, design patterns, scalability, distributed systems, and comprehensive solution design for complex software systems.

## When to Use This Skill
- Designing new systems from scratch
- Architecting microservices
- Planning for scale and performance
- Distributed systems design
- Technology stack selection
- System migration planning
- Capacity planning
- Architecture documentation

## Core Design Principles

### 1. SOLID Principles
- **S**ingle Responsibility Principle
- **O**pen/Closed Principle
- **L**iskov Substitution Principle
- **I**nterface Segregation Principle
- **D**ependency Inversion Principle

### 2. Scalability Patterns
- Horizontal Scaling (Scale Out)
- Vertical Scaling (Scale Up)
- Load Balancing
- Caching Strategies
- Database Sharding
- Read Replicas
- CDN for Static Assets

### 3. Reliability Patterns
- Circuit Breaker
- Retry with Exponential Backoff
- Bulkhead Pattern
- Health Checks
- Graceful Degradation
- Failover Mechanisms

### 4. Design Patterns

#### Creational Patterns
- **Singleton** - Single instance
- **Factory** - Object creation interface
- **Builder** - Complex object construction
- **Prototype** - Clone existing objects

#### Structural Patterns
- **Adapter** - Interface compatibility
- **Decorator** - Add behavior dynamically
- **Facade** - Simplified interface
- **Proxy** - Placeholder for another object

#### Behavioral Patterns
- **Observer** - Event notification
- **Strategy** - Interchangeable algorithms
- **Command** - Encapsulate requests
- **State** - Behavior based on state

## System Design Process

### 1. Requirements Gathering
```
Functional Requirements:
- What features does the system need?
- What are the core user flows?
- What operations are most critical?

Non-Functional Requirements:
- Expected traffic (QPS, DAU, MAU)
- Data volume and growth
- Latency requirements (p50, p95, p99)
- Availability requirements (99.9%, 99.99%)
- Consistency requirements
- Security requirements
```

### 2. Capacity Planning
```
Calculate:
- Storage: Users × Data per user × Growth rate
- Bandwidth: Requests/sec × Average request size
- Memory: Cache size + Active sessions
- Compute: CPU/Memory per request × QPS
```

### 3. High-Level Design
```
Components:
1. Client Layer (Web, Mobile, Desktop)
2. Edge Layer (CDN, Load Balancer)
3. Application Layer (API Servers, Services)
4. Data Layer (Databases, Caches, Queues)
5. External Services (Payment, Email, SMS)
```

### 4. API Design
```
RESTful API Example:
GET    /api/v1/users
POST   /api/v1/users
GET    /api/v1/users/{id}
PUT    /api/v1/users/{id}
DELETE /api/v1/users/{id}

Consider:
- Versioning strategy
- Authentication/Authorization
- Rate limiting
- Pagination
- Error handling
- HATEOAS (optional)
```

### 5. Data Model Design
```
Considerations:
- Normalized vs Denormalized
- SQL vs NoSQL
- Partitioning strategy
- Indexing strategy
- Data retention policy
- Backup and recovery
```

## Common System Designs

### 1. URL Shortener
```
Requirements:
- Shorten long URLs
- Redirect to original URL
- Track analytics
- Handle 100M URLs
- 100:1 read/write ratio

Design:
┌─────────┐      ┌──────────────┐      ┌──────────┐
│ Client  │─────▶│ Load Balancer│─────▶│ API Server│
└─────────┘      └──────────────┘      └─────┬────┘
                                             │
                        ┌────────────────────┼────────────┐
                        │                    │            │
                   ┌────▼────┐         ┌─────▼─────┐ ┌───▼────┐
                   │  Cache  │         │ Database  │ │Analytics│
                   │ (Redis) │         │(PostgreSQL│ │(ClickHouse)│
                   └─────────┘         └───────────┘ └────────┘

Key Design Decisions:
- Base62 encoding for short codes
- Redis for caching hot URLs
- PostgreSQL for persistence
- Separate analytics database
- Rate limiting per user
```

### 2. Social Media Feed
```
Requirements:
- Post creation and display
- Follow/unfollow users
- Timeline generation
- 500M users
- Sub-second feed load time

Design:
┌─────────┐     ┌──────────┐     ┌───────────┐
│ Mobile  │────▶│   CDN    │────▶│  API GW   │
└─────────┘     └──────────┘     └─────┬─────┘
                                       │
              ┌────────────────────────┼──────────────┐
              │                        │              │
        ┌─────▼─────┐          ┌──────▼──────┐  ┌────▼────┐
        │ User Svc  │          │  Post Svc   │  │Feed Svc │
        └─────┬─────┘          └──────┬──────┘  └────┬────┘
              │                       │              │
        ┌─────▼─────┐          ┌──────▼──────┐  ┌────▼────┐
        │ User DB   │          │  Post DB    │  │ Cache   │
        │(PostgreSQL│          │ (Cassandra) │  │(Redis)  │
        └───────────┘          └─────────────┘  └─────────┘

Key Design Decisions:
- Fan-out on write for small followings
- Fan-out on read for celebrities
- Cassandra for time-series data
- Redis for pre-computed feeds
- Separate microservices
```

### 3. E-Commerce Platform
```
Microservices Architecture:

┌────────────────────────────────────────────────────┐
│                   API Gateway                      │
└─────┬──────┬──────┬──────┬──────┬──────┬─────────┘
      │      │      │      │      │      │
   ┌──▼──┐┌──▼──┐┌──▼──┐┌──▼──┐┌──▼──┐┌──▼──┐
   │User ││Prod ││Cart ││Order││Pay  ││Inv  │
   │ Svc ││ Svc ││ Svc ││ Svc ││ Svc ││ Svc │
   └──┬──┘└──┬──┘└──┬──┘└──┬──┘└──┬──┘└──┬──┘
      │      │      │      │      │      │
   ┌──▼──────▼──────▼──────▼──────▼──────▼──┐
   │         Message Queue (RabbitMQ)       │
   └────────────────────────────────────────┘
      │      │      │      │      │      │
   ┌──▼──┐┌──▼──┐┌──▼──┐┌──▼──┐┌──▼──┐┌──▼──┐
   │User ││Prod ││Cart ││Order││Pay  ││Inv  │
   │ DB  ││ DB  ││Cache││ DB  ││Logs ││ DB  │
   └─────┘└─────┘└─────┘└─────┘└─────┘└─────┘

Event Flow:
1. User places order → OrderCreated event
2. Payment Service → PaymentProcessed event
3. Inventory Service → ItemsReserved event
4. Notification Service → OrderConfirmed email
```

## Architecture Patterns

### 1. Microservices
**Pros:**
- Independent deployment
- Technology diversity
- Scalability
- Fault isolation

**Cons:**
- Complexity
- Distributed system challenges
- Network latency
- Data consistency

### 2. Event-Driven Architecture
**Components:**
- Event Producers
- Event Bus (Kafka, RabbitMQ)
- Event Consumers
- Event Store

**Benefits:**
- Loose coupling
- Asynchronous processing
- Scalability
- Audit trail

### 3. CQRS (Command Query Responsibility Segregation)
```
Write Side:              Read Side:
┌─────────┐             ┌─────────┐
│ Command │             │  Query  │
│ Handler │             │ Handler │
└────┬────┘             └────┬────┘
     │                       │
┌────▼────┐             ┌────▼────┐
│ Write   │   Events    │  Read   │
│  DB     │────────────▶│  DB     │
└─────────┘             └─────────┘
```

### 4. Hexagonal Architecture (Ports & Adapters)
```
        ┌──────────────────────┐
        │   Application Core   │
        │   (Business Logic)   │
        └──────────┬───────────┘
                   │
        ┌──────────┴───────────┐
        │                      │
   ┌────▼────┐           ┌────▼────┐
   │  Ports  │           │  Ports  │
   │  (API)  │           │  (SPI)  │
   └────┬────┘           └────┬────┘
        │                     │
   ┌────▼────┐           ┌────▼────┐
   │Adapters │           │Adapters │
   │(HTTP,   │           │(DB,     │
   │ gRPC)   │           │ Queue)  │
   └─────────┘           └─────────┘
```

## Database Selection Guide

### SQL (PostgreSQL, MySQL)
**Use When:**
- ACID compliance required
- Complex joins and relationships
- Data integrity is critical
- Well-defined schema

### NoSQL Document (MongoDB)
**Use When:**
- Flexible schema
- Hierarchical data
- Rapid development
- Horizontal scaling

### NoSQL Key-Value (Redis, DynamoDB)
**Use When:**
- Simple key-based lookups
- Caching
- Session storage
- High-speed access

### NoSQL Wide-Column (Cassandra, HBase)
**Use When:**
- Time-series data
- Write-heavy workloads
- Massive scale
- High availability

### NoSQL Graph (Neo4j)
**Use When:**
- Relationship-heavy data
- Social networks
- Recommendation engines
- Fraud detection

## Performance Optimization

### Caching Strategies
```
1. Cache-Aside (Lazy Loading)
   - App checks cache first
   - On miss, load from DB and cache
   - Use for: Read-heavy workloads

2. Write-Through
   - Write to cache and DB together
   - Use for: Data consistency

3. Write-Behind
   - Write to cache first, DB later
   - Use for: Write-heavy workloads

4. Refresh-Ahead
   - Proactively refresh cache
   - Use for: Predictable access patterns
```

### CDN Strategy
- Static assets (images, CSS, JS)
- Geographic distribution
- Edge caching
- Reduced origin server load

### Database Optimization
- Proper indexing
- Query optimization
- Connection pooling
- Read replicas
- Partitioning/Sharding

## Security Considerations

1. **Authentication & Authorization**
   - JWT tokens
   - OAuth 2.0
   - Role-based access control (RBAC)
   - Principle of least privilege

2. **Data Protection**
   - Encryption at rest
   - Encryption in transit (TLS)
   - Sensitive data masking
   - PII compliance

3. **API Security**
   - Rate limiting
   - Input validation
   - CORS configuration
   - API keys/tokens

4. **Infrastructure Security**
   - Network segmentation
   - Firewalls
   - DDoS protection
   - Security groups

## Monitoring & Observability

### The Three Pillars

1. **Metrics**
   - Response time (p50, p95, p99)
   - Error rate
   - Request rate
   - Resource utilization

2. **Logs**
   - Structured logging
   - Centralized logging (ELK)
   - Log levels
   - Correlation IDs

3. **Traces**
   - Distributed tracing
   - Request flow visualization
   - Bottleneck identification
   - Service dependencies

## Best Practices

1. **Start Simple** - Don't over-engineer
2. **Design for Failure** - Assume components will fail
3. **Eventual Consistency** - Accept when appropriate
4. **Idempotency** - Make operations repeatable safely
5. **API Versioning** - Plan for changes
6. **Documentation** - Keep architecture docs updated
7. **Cost Optimization** - Consider operational costs
8. **Testing Strategy** - Unit, integration, E2E tests

## Architecture Decision Records (ADR)

Template:
```markdown
# ADR-001: Use PostgreSQL for Primary Database

## Status
Accepted

## Context
We need a reliable database for our e-commerce platform
handling transactions and user data.

## Decision
We will use PostgreSQL as our primary database.

## Consequences
Pros:
- ACID compliance
- Strong consistency
- Mature ecosystem

Cons:
- Vertical scaling limits
- Complex sharding setup

## Alternatives Considered
- MongoDB - rejected due to consistency requirements
- MySQL - rejected due to PostgreSQL's better JSON support
```

This skill ensures well-architected, scalable, and maintainable systems designed for long-term success.

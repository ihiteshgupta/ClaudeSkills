---
name: technical-writer
description: Expert in creating comprehensive technical documentation, API docs, user guides, and developer documentation. Use for writing clear, professional technical content.
allowed-tools: Read, Write, Edit, Grep, Glob, Bash
---

# Technical Writing Expert

## Purpose
Create clear, comprehensive, and user-friendly technical documentation including API documentation, user guides, architecture docs, and developer resources.

## When to Use This Skill
- Writing API documentation
- Creating README files
- Documenting system architecture
- Writing user guides and tutorials
- Creating onboarding documentation
- Documenting code and design decisions
- Writing release notes
- Creating runbooks and playbooks

## Documentation Types

### 1. README Files
```markdown
# Project Name

Brief description of what this project does and who it's for.

## Features

- Feature 1
- Feature 2
- Feature 3

## Prerequisites

- Node.js 18+
- PostgreSQL 14+
- Docker (optional)

## Installation

\`\`\`bash
npm install
cp .env.example .env
npm run db:migrate
\`\`\`

## Usage

\`\`\`javascript
import { ServiceName } from 'package-name';

const service = new ServiceName({
  apiKey: 'your-api-key'
});
\`\`\`

## Configuration

| Variable | Description | Default |
|----------|-------------|---------|
| PORT | Server port | 3000 |
| DATABASE_URL | Database connection | - |

## Development

\`\`\`bash
npm run dev      # Start development server
npm test        # Run tests
npm run build   # Build for production
\`\`\`

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md)

## License

MIT
```

### 2. API Documentation
```markdown
# API Reference

Base URL: `https://api.example.com/v1`

## Authentication

All API requests require an API key in the header:

\`\`\`
Authorization: Bearer YOUR_API_KEY
\`\`\`

## Endpoints

### Create User

Creates a new user account.

**Endpoint:** `POST /users`

**Request Body:**
\`\`\`json
{
  "email": "user@example.com",
  "password": "secure_password",
  "name": "John Doe"
}
\`\`\`

**Response:** `201 Created`
\`\`\`json
{
  "id": "usr_123",
  "email": "user@example.com",
  "name": "John Doe",
  "created_at": "2024-01-15T10:30:00Z"
}
\`\`\`

**Error Responses:**

- `400 Bad Request` - Invalid input
- `409 Conflict` - Email already exists
- `500 Internal Server Error` - Server error

**Example:**
\`\`\`bash
curl -X POST https://api.example.com/v1/users \\
  -H "Content-Type: application/json" \\
  -d '{"email":"user@example.com","password":"pass123","name":"John"}'
\`\`\`

### Get User

Retrieves user information.

**Endpoint:** `GET /users/{userId}`

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| userId | string | Yes | The user ID |

**Response:** `200 OK`
\`\`\`json
{
  "id": "usr_123",
  "email": "user@example.com",
  "name": "John Doe",
  "created_at": "2024-01-15T10:30:00Z"
}
\`\`\`

## Rate Limiting

- 100 requests per minute per API key
- Headers: `X-RateLimit-Limit`, `X-RateLimit-Remaining`

## Pagination

\`\`\`
GET /users?page=2&limit=20
\`\`\`

Response includes pagination metadata:
\`\`\`json
{
  "data": [...],
  "pagination": {
    "page": 2,
    "limit": 20,
    "total": 150,
    "total_pages": 8
  }
}
\`\`\`

## Error Handling

All errors follow this format:
\`\`\`json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid email format",
    "details": {
      "field": "email"
    }
  }
}
\`\`\`
```

### 3. Architecture Documentation
```markdown
# System Architecture

## Overview

High-level description of the system architecture and design goals.

## Architecture Diagram

\`\`\`mermaid
graph TB
    Client[Client Apps] --> LB[Load Balancer]
    LB --> API[API Gateway]
    API --> Auth[Auth Service]
    API --> User[User Service]
    User --> DB[(Database)]
\`\`\`

## Components

### API Gateway
**Responsibility:** Route requests, authentication, rate limiting

**Technology:** Kong

**Endpoints:**
- `/api/v1/*` - Main API
- `/auth/*` - Authentication

### User Service
**Responsibility:** User management and profile operations

**Technology:** Node.js, Express

**Database:** PostgreSQL

**API:**
- `POST /users` - Create user
- `GET /users/:id` - Get user
- `PUT /users/:id` - Update user

## Data Flow

1. Client sends request to Load Balancer
2. Load Balancer routes to API Gateway
3. API Gateway validates authentication
4. Request forwarded to appropriate service
5. Service processes and returns response

## Security

- TLS 1.3 for all communications
- JWT tokens for authentication
- API key for service-to-service auth
- Rate limiting: 100 req/min per user

## Scalability

- Horizontal scaling of API services
- Database read replicas
- Redis caching layer
- CDN for static assets

## Monitoring

- Prometheus for metrics
- Grafana dashboards
- ELK stack for logs
- PagerDuty for alerts
```

### 4. User Guide
```markdown
# User Guide

## Getting Started

Welcome to [Product Name]! This guide will help you get started.

### Step 1: Create an Account

1. Visit [https://app.example.com/signup](https://app.example.com/signup)
2. Enter your email and password
3. Verify your email address
4. Complete your profile

### Step 2: Set Up Your First Project

1. Click "New Project" in the dashboard
2. Enter project details
3. Invite team members (optional)
4. Click "Create"

## Features

### Feature 1: Project Management

Description of how to use this feature.

**To create a task:**

1. Navigate to your project
2. Click "New Task"
3. Fill in task details
4. Assign to team member
5. Set due date
6. Click "Save"

**Tips:**
- Use labels to organize tasks
- Set priorities for important items
- Add comments for collaboration

### Feature 2: Collaboration

Description of collaboration features.

## Troubleshooting

### Cannot Log In

**Problem:** Forgot password

**Solution:**
1. Click "Forgot Password" on login page
2. Enter your email
3. Check email for reset link
4. Create new password

### Upload Fails

**Problem:** File upload fails

**Common causes:**
- File too large (max 10MB)
- Unsupported file type
- Network connection issue

**Solution:**
1. Check file size and type
2. Ensure stable internet connection
3. Try again
4. Contact support if issue persists

## FAQ

**Q: How do I delete my account?**
A: Go to Settings > Account > Delete Account

**Q: Can I export my data?**
A: Yes, go to Settings > Data > Export

## Support

- Email: support@example.com
- Documentation: https://docs.example.com
- Community: https://community.example.com
```

### 5. Runbook
```markdown
# Production Runbook

## Service Overview

**Name:** User Service
**Repository:** github.com/company/user-service
**Deployment:** Kubernetes cluster (production)
**On-Call:** #team-backend

## Health Checks

- Service health: `https://api.example.com/health`
- Database: `https://api.example.com/health/db`
- Expected response: `{"status": "healthy"}`

## Common Issues

### High Error Rate

**Symptoms:**
- Error rate > 5%
- Increased 5xx responses

**Investigation:**
1. Check Grafana dashboard: [link]
2. Review error logs: `kubectl logs -l app=user-service --tail=100`
3. Check database connections: `SHOW PROCESSLIST;`

**Resolution:**
1. If database connections maxed out:
   - Restart pods: `kubectl rollout restart deployment/user-service`
   - Scale up if needed: `kubectl scale deployment/user-service --replicas=5`

2. If third-party API down:
   - Enable circuit breaker
   - Switch to degraded mode

### High Latency

**Symptoms:**
- p95 latency > 500ms
- Slow response times

**Investigation:**
1. Check APM traces
2. Review slow queries
3. Check cache hit rate

**Resolution:**
1. Increase cache TTL
2. Add database indexes
3. Scale horizontally

### Deployment Issues

**Rollback Procedure:**
\`\`\`bash
# Get previous revision
kubectl rollout history deployment/user-service

# Rollback to previous version
kubectl rollout undo deployment/user-service

# Rollback to specific revision
kubectl rollout undo deployment/user-service --to-revision=2
\`\`\`

## Monitoring

**Dashboards:**
- [Service Dashboard](link)
- [Database Dashboard](link)

**Alerts:**
- High error rate (>5%)
- High latency (p95 > 500ms)
- Low disk space (<10%)

**Key Metrics:**
- Request rate
- Error rate
- Response time (p50, p95, p99)
- CPU/Memory usage

## Deployment

\`\`\`bash
# Build and tag image
docker build -t user-service:v1.2.3 .

# Push to registry
docker push registry.example.com/user-service:v1.2.3

# Update Kubernetes
kubectl set image deployment/user-service \\
  user-service=registry.example.com/user-service:v1.2.3

# Watch rollout
kubectl rollout status deployment/user-service
\`\`\`

## Contacts

- Team Lead: @john
- On-Call: #oncall-backend
- DevOps: @devops-team
```

## Writing Principles

### 1. Clarity
- Use simple, clear language
- Avoid jargon when possible
- Define technical terms
- Use active voice

### 2. Structure
- Start with overview
- Use headings and subheadings
- Include table of contents for long docs
- Use lists and tables

### 3. Completeness
- Cover all necessary information
- Include examples
- Provide context
- Address common questions

### 4. Consistency
- Consistent terminology
- Consistent formatting
- Follow style guide
- Use templates

### 5. Maintainability
- Keep docs with code
- Version documentation
- Include last updated date
- Regular reviews

## Documentation Checklist

- [ ] Clear title and description
- [ ] Prerequisites listed
- [ ] Installation instructions
- [ ] Configuration options documented
- [ ] Code examples provided
- [ ] Error messages explained
- [ ] Troubleshooting section
- [ ] Links verified
- [ ] Grammar and spelling checked
- [ ] Screenshots/diagrams included
- [ ] Up-to-date with current version

## Best Practices

1. **Write for Your Audience** - Consider technical level
2. **Show, Don't Tell** - Use examples
3. **Keep It Updated** - Review regularly
4. **Get Feedback** - Have others review
5. **Use Version Control** - Track changes
6. **Include Diagrams** - Visual aids help
7. **Test Your Examples** - Ensure they work
8. **Link Related Docs** - Cross-reference

## Tools

- **Markdown** - Most documentation
- **OpenAPI/Swagger** - API documentation
- **JSDoc/TSDoc** - Code documentation
- **Mermaid** - Diagrams
- **Docusaurus** - Documentation sites
- **GitBook** - Knowledge bases
- **Notion** - Collaborative docs

This skill ensures comprehensive, clear, and maintainable documentation that serves both users and developers effectively.

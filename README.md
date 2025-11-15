# Claude Code Agent Skills Marketplace

A comprehensive collection of Claude Code Agent skills designed for fullstack engineers, covering everything from frontend to cloud deployment.

## 🎯 Overview

This marketplace provides ready-to-use skills for Claude Code Agent that enhance productivity across the entire software development lifecycle. Each skill is carefully crafted to follow best practices and progressive disclosure principles.

## 📚 Skills Categories

### Frontend Development
- **react-expert** - React development with hooks, context, and modern patterns
- **vue-expert** - Vue.js 3 composition API and best practices
- **angular-expert** - Angular development with TypeScript
- **css-expert** - Modern CSS, Tailwind, and responsive design
- **accessibility** - WCAG compliance and accessible UI development

### Backend Development
- **nodejs-expert** - Node.js and Express.js development
- **python-backend** - Python backend with FastAPI/Django/Flask
- **java-spring** - Spring Boot and Java backend development
- **go-backend** - Go backend services and APIs
- **dotnet-expert** - .NET Core and C# backend development

### Database
- **sql-expert** - SQL database design and optimization
- **nosql-expert** - MongoDB, Redis, DynamoDB expertise
- **database-migration** - Schema migrations and data management
- **orm-expert** - Prisma, TypeORM, SQLAlchemy, Hibernate

### API Development
- **rest-api** - RESTful API design and implementation
- **graphql-expert** - GraphQL schema design and resolvers
- **grpc-expert** - gRPC service development
- **api-documentation** - OpenAPI/Swagger documentation

### Testing
- **unit-testing** - Jest, Pytest, JUnit unit testing
- **integration-testing** - Integration test strategies
- **e2e-testing** - Playwright, Cypress, Selenium
- **tdd-expert** - Test-driven development practices

### Documentation
- **technical-writer** - Technical documentation and guides
- **api-docs** - API documentation and developer portals
- **readme-expert** - Compelling README files
- **code-comments** - Effective code documentation

### Solution Architecture
- **system-design** - System architecture and design patterns
- **microservices** - Microservices architecture
- **event-driven** - Event-driven architecture patterns
- **scalability** - High-scale system design

### Cloud Platforms

#### AWS
- **aws-architect** - AWS solutions architecture
- **aws-lambda** - Serverless with Lambda
- **aws-ecs-fargate** - Container orchestration on AWS
- **aws-rds** - Database services on AWS
- **aws-security** - AWS security best practices

#### GCP
- **gcp-architect** - Google Cloud Platform architecture
- **gcp-cloud-run** - Serverless containers on GCP
- **gcp-bigquery** - BigQuery data warehousing
- **gcp-security** - GCP security and IAM

#### Azure
- **azure-architect** - Azure solutions architecture
- **azure-functions** - Azure serverless computing
- **azure-aks** - Azure Kubernetes Service
- **azure-security** - Azure security best practices

### Kubernetes & Containers
- **kubernetes-expert** - Kubernetes deployment and management
- **docker-expert** - Docker containerization
- **helm-charts** - Helm chart development
- **k8s-security** - Kubernetes security hardening

### DevOps & CI/CD
- **cicd-expert** - CI/CD pipeline design
- **github-actions** - GitHub Actions workflows
- **gitlab-ci** - GitLab CI/CD
- **terraform** - Infrastructure as Code with Terraform
- **ansible** - Configuration management with Ansible

### UI/UX Design
- **ux-designer** - User experience design principles
- **ui-designer** - User interface design
- **design-systems** - Design system creation
- **figma-expert** - Figma design collaboration

### Diagrams & Visualization
- **mermaid-diagrams** - Mermaid diagram creation
- **plantuml-expert** - PlantUML diagrams
- **architecture-diagrams** - System architecture visualization
- **data-flow-diagrams** - Data flow visualization

### Security
- **security-expert** - Application security best practices
- **owasp-expert** - OWASP Top 10 mitigation
- **penetration-testing** - Security testing and auditing
- **secrets-management** - Secrets and credentials management

### Performance
- **performance-optimization** - Application performance tuning
- **caching-strategies** - Caching implementation
- **database-optimization** - Database query optimization
- **frontend-performance** - Frontend performance optimization

### Monitoring & Observability
- **observability** - Logging, metrics, and tracing
- **prometheus-grafana** - Prometheus and Grafana setup
- **elk-stack** - ELK stack configuration
- **apm-expert** - Application performance monitoring

## 🚀 Installation

### Install All Skills
```bash
# Clone this repository
git clone <repository-url>

# Copy skills to Claude Code skills directory
cp -r skills/* ~/.config/claude/skills/
```

### Install Specific Category
```bash
# Install only cloud skills
cp -r skills/cloud/* ~/.config/claude/skills/

# Install only frontend skills
cp -r skills/frontend/* ~/.config/claude/skills/
```

### Install Individual Skill
```bash
# Install specific skill
cp -r skills/diagrams/mermaid-diagrams ~/.config/claude/skills/
```

## 📖 Usage

Skills are automatically available in Claude Code once installed. Claude will intelligently invoke the appropriate skill based on your request.

### Examples

```
# Frontend development
"Build a React component with TypeScript and proper error handling"

# Cloud deployment
"Deploy this application to AWS using ECS Fargate with auto-scaling"

# Documentation
"Create comprehensive API documentation for these endpoints"

# Diagrams
"Create a mermaid sequence diagram showing the authentication flow"

# Architecture
"Design a microservices architecture for this e-commerce platform"
```

## 🎨 Skill Structure

Each skill follows this structure:
```
skill-name/
├── SKILL.md          # Main skill definition with YAML frontmatter
├── REFERENCE.md      # (Optional) Reference documentation
├── FORMS.md          # (Optional) Templates and forms
└── scripts/          # (Optional) Helper scripts
```

## 🤝 Contributing

Contributions are welcome! Please follow these guidelines:

1. **Follow the Standard Structure** - Use YAML frontmatter with name, description, and allowed-tools
2. **Be Specific** - Skills should have clear, focused purposes
3. **Include Examples** - Provide usage examples in the skill description
4. **Test Thoroughly** - Ensure skills work as expected with Claude Code
5. **Document Well** - Include clear instructions and best practices

### Creating a New Skill

1. Create a new directory under the appropriate category
2. Add `SKILL.md` with proper YAML frontmatter
3. Include clear instructions and best practices
4. Add reference materials if needed
5. Test with Claude Code
6. Submit a pull request

## 📋 Requirements

- Claude Code (latest version)
- Access to Claude API

## 🔧 Customization

Skills can be customized by:
- Editing the SKILL.md file to adjust instructions
- Adding project-specific context in REFERENCE.md
- Creating custom templates in FORMS.md
- Adding helper scripts for automation

## 📄 License

MIT License - See LICENSE file for details

## 🌟 Popular Skills

- **mermaid-diagrams** - Generate beautiful diagrams from text
- **aws-architect** - Complete AWS solutions architecture
- **react-expert** - Modern React development
- **kubernetes-expert** - K8s deployment and management
- **security-expert** - Application security hardening
- **technical-writer** - Professional documentation

## 📞 Support

For issues, questions, or contributions:
- Open an issue on GitHub
- Submit a pull request
- Check existing skills for examples

## 🗺️ Roadmap

- [ ] Machine Learning & AI skills
- [ ] Mobile development (React Native, Flutter)
- [ ] Game development
- [ ] Blockchain development
- [ ] Edge computing
- [ ] Quantum computing basics
- [ ] More language-specific skills

---

**Made with ❤️ for the Claude Code community**

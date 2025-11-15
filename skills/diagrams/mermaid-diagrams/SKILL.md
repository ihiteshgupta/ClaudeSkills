---
name: mermaid-diagrams
description: Expert in creating Mermaid diagrams for documentation - flowcharts, sequence diagrams, class diagrams, ERD, Gantt charts, and more. Use when visualizing system architecture, workflows, or data flows.
allowed-tools: Read, Write, Edit, Grep, Glob, Bash
---

# Mermaid Diagram Expert

## Purpose
Create professional, comprehensive Mermaid diagrams for technical documentation, system architecture, data flows, and process visualization in markdown files.

## When to Use This Skill
- Documenting system architecture
- Creating sequence diagrams for API flows
- Designing database schemas (ERD)
- Visualizing state machines
- Planning project timelines (Gantt)
- Mapping user journeys
- Documenting class hierarchies
- Creating flowcharts for business logic

## Diagram Types & Templates

### 1. Flowchart
```mermaid
flowchart TD
    Start([Start]) --> Input[/User Input/]
    Input --> Validate{Valid?}
    Validate -->|Yes| Process[Process Data]
    Validate -->|No| Error[Show Error]
    Error --> Input
    Process --> Save[(Save to DB)]
    Save --> Success([Success])

    style Start fill:#90EE90
    style Success fill:#90EE90
    style Error fill:#FFB6C1
    style Save fill:#87CEEB
```

**Common Shapes:**
- `[Rectangle]` - Process
- `([Rounded])` - Terminal/Start/End
- `{Diamond}` - Decision
- `[(Database)]` - Database
- `[[Subroutine]]` - Predefined process
- `[/Parallelogram/]` - Input/Output
- `((Circle))` - Connector

### 2. Sequence Diagram
```mermaid
sequenceDiagram
    actor User
    participant Frontend
    participant API
    participant Auth
    participant Database

    User->>Frontend: Login Request
    Frontend->>API: POST /auth/login
    activate API
    API->>Auth: Validate Credentials
    activate Auth
    Auth->>Database: Query User
    Database-->>Auth: User Data
    Auth-->>API: JWT Token
    deactivate Auth
    API-->>Frontend: 200 OK + Token
    deactivate API
    Frontend->>Frontend: Store Token
    Frontend-->>User: Redirect to Dashboard

    Note over User,Database: Authentication Flow Complete
```

**Key Features:**
- `actor` - Human participants
- `participant` - System components
- `->` - Solid line (synchronous)
- `-->` - Dashed line (response)
- `->>` - Solid arrow
- `-->>` - Dashed arrow
- `activate/deactivate` - Lifeline activation
- `Note` - Annotations

### 3. Class Diagram
```mermaid
classDiagram
    class User {
        -String id
        -String email
        -String password
        +login()
        +logout()
        +updateProfile()
    }

    class Order {
        -String orderId
        -Date createdAt
        -OrderStatus status
        +createOrder()
        +cancelOrder()
        +getTotal()
    }

    class Product {
        -String productId
        -String name
        -Decimal price
        -Integer stock
        +updateStock()
        +getPrice()
    }

    class OrderItem {
        -Integer quantity
        -Decimal price
        +getSubtotal()
    }

    User "1" --> "*" Order : places
    Order "1" --> "*" OrderItem : contains
    Product "1" --> "*" OrderItem : included in

    <<interface>> PaymentProcessor
    PaymentProcessor <|.. StripeProcessor
    PaymentProcessor <|.. PayPalProcessor
```

**Relationships:**
- `<|--` - Inheritance
- `*--` - Composition
- `o--` - Aggregation
- `-->` - Association
- `..>` - Dependency
- `<|..` - Realization

### 4. Entity Relationship Diagram (ERD)
```mermaid
erDiagram
    USER ||--o{ ORDER : places
    USER {
        uuid id PK
        string email UK
        string password_hash
        timestamp created_at
    }

    ORDER ||--|{ ORDER_ITEM : contains
    ORDER {
        uuid id PK
        uuid user_id FK
        decimal total
        enum status
        timestamp created_at
    }

    PRODUCT ||--o{ ORDER_ITEM : "ordered in"
    PRODUCT {
        uuid id PK
        string name
        text description
        decimal price
        integer stock
    }

    ORDER_ITEM {
        uuid id PK
        uuid order_id FK
        uuid product_id FK
        integer quantity
        decimal unit_price
    }
```

**Cardinality:**
- `||--||` - One to One
- `||--o{` - One to Many
- `}o--o{` - Many to Many
- `||--o|` - One to Zero or One

### 5. State Diagram
```mermaid
stateDiagram-v2
    [*] --> Draft
    Draft --> Review : Submit
    Review --> Approved : Accept
    Review --> Rejected : Reject
    Review --> Draft : Request Changes
    Rejected --> Draft : Revise
    Approved --> Published : Publish
    Published --> Archived : Archive
    Archived --> [*]

    state Review {
        [*] --> PeerReview
        PeerReview --> TechnicalReview
        TechnicalReview --> FinalReview
        FinalReview --> [*]
    }

    note right of Published
        Content is live
        and accessible
    end note
```

### 6. Gantt Chart
```mermaid
gantt
    title Project Development Timeline
    dateFormat YYYY-MM-DD
    section Planning
    Requirements Gathering    :done, req, 2024-01-01, 2024-01-15
    System Design             :done, design, 2024-01-10, 2024-01-25

    section Development
    Backend API               :active, backend, 2024-01-20, 30d
    Frontend UI               :frontend, 2024-02-01, 25d
    Database Setup            :done, db, 2024-01-20, 10d

    section Testing
    Unit Testing              :testing, after backend, 10d
    Integration Testing       :integration, after frontend, 15d

    section Deployment
    Staging Deployment        :staging, after integration, 5d
    Production Deployment     :prod, after staging, 3d
```

### 7. Git Graph
```mermaid
gitGraph
    commit id: "Initial commit"
    commit id: "Add user authentication"
    branch develop
    checkout develop
    commit id: "Setup database"
    branch feature/api
    checkout feature/api
    commit id: "Create REST endpoints"
    commit id: "Add validation"
    checkout develop
    merge feature/api
    checkout main
    merge develop tag: "v1.0.0"
    commit id: "Hotfix security issue"
```

### 8. Architecture Diagram (C4 Model)
```mermaid
graph TB
    subgraph "Client Layer"
        Web[Web Application<br/>React]
        Mobile[Mobile App<br/>React Native]
    end

    subgraph "API Gateway"
        Gateway[API Gateway<br/>Kong/nginx]
    end

    subgraph "Microservices"
        Auth[Auth Service<br/>Node.js]
        User[User Service<br/>Node.js]
        Order[Order Service<br/>Python]
        Payment[Payment Service<br/>Go]
    end

    subgraph "Data Layer"
        UserDB[(User DB<br/>PostgreSQL)]
        OrderDB[(Order DB<br/>MongoDB)]
        Cache[(Redis Cache)]
    end

    subgraph "External Services"
        Stripe[Stripe API]
        Email[Email Service<br/>SendGrid]
    end

    Web --> Gateway
    Mobile --> Gateway
    Gateway --> Auth
    Gateway --> User
    Gateway --> Order
    Gateway --> Payment

    Auth --> UserDB
    User --> UserDB
    Order --> OrderDB
    Payment --> Stripe

    Auth --> Cache
    User --> Email

    style Web fill:#61dafb
    style Mobile fill:#61dafb
    style Gateway fill:#f9c74f
    style Auth fill:#90be6d
    style User fill:#90be6d
    style Order fill:#90be6d
    style Payment fill:#90be6d
```

### 9. User Journey
```mermaid
journey
    title User Purchase Journey
    section Discovery
      Browse Products: 5: User
      Search Product: 4: User
      View Details: 5: User
    section Evaluation
      Compare Options: 3: User
      Read Reviews: 4: User
      Check Price: 5: User
    section Purchase
      Add to Cart: 5: User
      Apply Coupon: 3: User, System
      Checkout: 4: User, System
      Payment: 3: User, System, Payment Gateway
    section Post-Purchase
      Order Confirmation: 5: User, System
      Track Shipment: 4: User, System
      Receive Product: 5: User
```

### 10. Mindmap
```mermaid
mindmap
  root((Fullstack<br/>Engineer))
    Frontend
      React
        Hooks
        Context
        Redux
      Vue
      CSS
        Tailwind
        Flexbox
        Grid
    Backend
      Node.js
        Express
        NestJS
      Python
        Django
        FastAPI
      Databases
        PostgreSQL
        MongoDB
    DevOps
      Docker
      Kubernetes
      CI/CD
        GitHub Actions
        GitLab CI
    Cloud
      AWS
        EC2
        Lambda
        RDS
      GCP
      Azure
```

## Advanced Patterns

### Microservices Architecture
```mermaid
flowchart TB
    subgraph "Client Applications"
        WebApp[Web App]
        MobileApp[Mobile App]
    end

    subgraph "Edge Layer"
        CDN[CDN<br/>CloudFront]
        LB[Load Balancer]
    end

    subgraph "API Layer"
        Gateway[API Gateway]
        Auth[Auth Middleware]
    end

    subgraph "Service Mesh"
        direction TB
        UserSvc[User Service]
        ProductSvc[Product Service]
        OrderSvc[Order Service]
        NotificationSvc[Notification Service]
    end

    subgraph "Data Stores"
        PrimaryDB[(Primary DB<br/>PostgreSQL)]
        CacheStore[(Redis)]
        MessageQueue[Message Queue<br/>RabbitMQ]
    end

    subgraph "Observability"
        Metrics[Prometheus]
        Logs[ELK Stack]
        Tracing[Jaeger]
    end

    WebApp --> CDN
    MobileApp --> CDN
    CDN --> LB
    LB --> Gateway
    Gateway --> Auth
    Auth --> UserSvc
    Auth --> ProductSvc
    Auth --> OrderSvc

    UserSvc --> PrimaryDB
    ProductSvc --> CacheStore
    OrderSvc --> MessageQueue
    MessageQueue --> NotificationSvc

    UserSvc -.-> Metrics
    ProductSvc -.-> Logs
    OrderSvc -.-> Tracing
```

### Event-Driven Architecture
```mermaid
sequenceDiagram
    participant User
    participant OrderService
    participant EventBus
    participant PaymentService
    participant InventoryService
    participant NotificationService

    User->>OrderService: Create Order
    activate OrderService
    OrderService->>EventBus: Publish OrderCreated Event
    deactivate OrderService
    OrderService-->>User: Order ID

    EventBus->>PaymentService: OrderCreated Event
    activate PaymentService
    PaymentService->>PaymentService: Process Payment
    PaymentService->>EventBus: Publish PaymentProcessed Event
    deactivate PaymentService

    EventBus->>InventoryService: PaymentProcessed Event
    activate InventoryService
    InventoryService->>InventoryService: Reserve Items
    InventoryService->>EventBus: Publish ItemsReserved Event
    deactivate InventoryService

    EventBus->>NotificationService: ItemsReserved Event
    activate NotificationService
    NotificationService->>User: Send Confirmation Email
    deactivate NotificationService
```

## Styling and Themes

### Custom Styling
```mermaid
%%{init: {'theme':'dark', 'themeVariables': { 'primaryColor':'#ff6b6b', 'primaryTextColor':'#fff', 'primaryBorderColor':'#ff6b6b', 'lineColor':'#f39c12', 'secondaryColor':'#4ecdc4', 'tertiaryColor':'#45b7d1'}}}%%
flowchart LR
    A[Start] --> B{Decision}
    B -->|Option 1| C[Result 1]
    B -->|Option 2| D[Result 2]
```

### Available Themes
- `default` - Standard theme
- `dark` - Dark mode
- `forest` - Green forest theme
- `neutral` - Neutral colors
- `base` - Minimal styling

## Best Practices

1. **Clear Naming** - Use descriptive node names
2. **Logical Flow** - Top-to-bottom or left-to-right
3. **Consistent Styling** - Use colors meaningfully
4. **Appropriate Detail** - Match complexity to audience
5. **Documentation** - Add notes for complex sections
6. **Accessibility** - Use sufficient contrast
7. **Version Control** - Keep diagrams with code
8. **Update Regularly** - Keep diagrams in sync with code

## Common Use Cases

### 1. API Authentication Flow
```mermaid
sequenceDiagram
    actor User
    participant Client
    participant API
    participant AuthService
    participant Database

    User->>Client: Enter Credentials
    Client->>API: POST /auth/login
    API->>AuthService: Validate Credentials
    AuthService->>Database: Query User
    Database-->>AuthService: User Found
    AuthService->>AuthService: Generate JWT
    AuthService-->>API: JWT Token
    API-->>Client: {token, user}
    Client->>Client: Store Token
    Client-->>User: Login Success

    Note over Client,API: Subsequent Requests

    User->>Client: Request Protected Resource
    Client->>API: GET /api/data<br/>Authorization: Bearer {token}
    API->>AuthService: Verify Token
    AuthService-->>API: Token Valid
    API-->>Client: Protected Data
    Client-->>User: Display Data
```

### 2. Database Schema Design
```mermaid
erDiagram
    USER ||--o{ POST : creates
    USER ||--o{ COMMENT : writes
    POST ||--o{ COMMENT : has
    POST }o--o{ TAG : tagged_with
    USER ||--o{ FOLLOWER : follows
    USER ||--o{ FOLLOWER : followed_by

    USER {
        uuid id PK
        string username UK
        string email UK
        string password_hash
        string avatar_url
        timestamp created_at
        timestamp updated_at
    }

    POST {
        uuid id PK
        uuid author_id FK
        string title
        text content
        enum status
        integer view_count
        timestamp published_at
        timestamp created_at
    }

    COMMENT {
        uuid id PK
        uuid post_id FK
        uuid author_id FK
        uuid parent_id FK
        text content
        timestamp created_at
    }

    TAG {
        uuid id PK
        string name UK
        string slug UK
    }

    FOLLOWER {
        uuid follower_id FK
        uuid following_id FK
        timestamp created_at
    }
```

### 3. CI/CD Pipeline
```mermaid
flowchart LR
    A[Code Push] --> B[GitHub Actions<br/>Triggered]
    B --> C{Tests Pass?}
    C -->|No| D[Notify Developer]
    C -->|Yes| E[Build Docker Image]
    E --> F[Push to Registry]
    F --> G{Branch?}
    G -->|develop| H[Deploy to Staging]
    G -->|main| I[Deploy to Production]
    H --> J[Run E2E Tests]
    J --> K{Tests Pass?}
    K -->|No| D
    K -->|Yes| L[Staging Complete]
    I --> M[Blue-Green Deploy]
    M --> N[Health Checks]
    N --> O{Healthy?}
    O -->|No| P[Rollback]
    O -->|Yes| Q[Production Complete]

    style C fill:#f9c74f
    style K fill:#f9c74f
    style O fill:#f9c74f
    style D fill:#e63946
    style P fill:#e63946
    style L fill:#06d6a0
    style Q fill:#06d6a0
```

## Integration with Documentation

Place Mermaid diagrams directly in markdown files:

```markdown
# System Architecture

Here's our microservices architecture:

\`\`\`mermaid
graph TB
    Client --> Gateway
    Gateway --> Services
\`\`\`
```

## Tips for Complex Diagrams

1. **Break into Subgraphs** - Group related components
2. **Use Consistent Direction** - TB (top-bottom) or LR (left-right)
3. **Color Code by Layer** - Different colors for different tiers
4. **Add Legends** - Explain symbols and colors
5. **Keep it Simple** - Multiple simple diagrams > one complex diagram

This skill enables creating comprehensive, professional diagrams that effectively communicate system designs, workflows, and architecture.

# SBI Compass

## Overview

SBI Compass is a customer journey intelligence platform designed for digital banking. It processes customer events, maintains historical context, and generates timely recommendations based on user goals and account activity.

The system follows an event-driven architecture where every meaningful interaction is evaluated against existing context before a recommendation is presented. Recommendations are explainable, traceable, and subject to business rules before reaching the customer.

---

# Architecture

```mermaid
flowchart TB

    Customer[Customer Application]
        --> API[Backend API]

    API --> UserService[User Service]
    API --> GoalService[Goal Service]
    API --> TransactionService[Transaction Service]

    UserService --> DB[(PostgreSQL)]
    GoalService --> DB
    TransactionService --> DB

    TransactionService --> Events[Event Queue]

    Events --> Observation[Observation Layer]
    Observation --> Memory[Context Store]
    Memory --> Planner[Planning Engine]
    Planner --> Policy[Policy Validation]
    Policy --> Recommendation[Recommendation Service]

    Recommendation --> Dashboard[Dashboard]
    Recommendation --> Notification[Notification Service]

    Dashboard --> Feedback[Customer Feedback]
    Feedback --> Memory
```

---

# Request Lifecycle

```mermaid
flowchart LR

    A[Customer Activity]
        --> B[Create Event]

    B --> C[Observation]

    C --> D[Load Historical Context]

    D --> E[Evaluate Current State]

    E --> F[Generate Recommendation]

    F --> G[Apply Business Rules]

    G --> H[Present to Customer]

    H --> I[Capture Feedback]

    I --> D
```

---

# Components

### Observation Layer

Receives application events and converts them into structured signals such as:

* Salary credited
* Recurring payment detected
* Goal updated
* Spending category changed
* Unusual transaction pattern

### Context Store

Maintains long-term information including:

* Financial goals
* Previous recommendations
* Customer preferences
* Historical feedback

### Planning Engine

Evaluates the current event together with historical context and determines the next recommended action.

### Policy Validation

Ensures recommendations satisfy internal business constraints before delivery.

### Recommendation Service

Publishes approved recommendations to customer-facing channels such as dashboards or notifications.

---

# Example Flow

```mermaid
sequenceDiagram

    participant Customer
    participant Backend
    participant Observation
    participant Context
    participant Planner
    participant Dashboard

    Customer->>Backend: Salary credited

    Backend->>Observation: Publish event

    Observation->>Context: Retrieve customer history

    Context-->>Planner: Goals and previous actions

    Planner->>Dashboard: Generate recommendation

    Customer->>Dashboard: Accept recommendation

    Dashboard->>Context: Record feedback
```

---

# Context Management

```mermaid
flowchart TD

    Transactions --> Observation

    Goals --> Context

    Preferences --> Context

    Feedback --> Context

    Observation --> Planner

    Context --> Planner

    Planner --> Recommendation

    Recommendation --> Customer

    Customer --> Feedback
```

---

# Governance

```mermaid
flowchart TD

    Recommendation --> Consent{Consent Available?}

    Consent -->|No| Skip[Do Not Display]

    Consent -->|Yes| Rules{Business Rules}

    Rules -->|Fail| Suppress[Suppress]

    Rules -->|Pass| Display[Display Recommendation]
```

---

# Technology Stack

| Layer          | Implementation      |
| -------------- | ------------------- |
| Frontend       | Next.js, TypeScript |
| Backend        | Go (Gin)            |
| Data Store     | PostgreSQL          |
| Cache          | Redis               |
| AI Service     | Python (FastAPI)    |
| Vector Storage | pgvector            |
| Authentication | JWT                 |
| Deployment     | Docker              |

---

# Repository Structure

```text
sbi-compass/
├── frontend/
├── backend/
├── ai-service/
├── docs/
├── infrastructure/
├── docker-compose.yml
└── README.md
```

---

# Development Roadmap

* Initial project setup
* Event ingestion pipeline
* Context storage
* Recommendation engine
* Customer dashboard
* Administrative reporting
* Deployment pipeline

---

# Notes

* Customer consent is required before generating personalized recommendations.
* Feedback from previous interactions influences future recommendations.
* Recommendations are evaluated against policy rules before presentation.
* The platform is designed to support incremental feature additions without changing the overall architecture.

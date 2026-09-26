# Open CMDB

An open-source Integration, ETL, CMDB, and Service Model platform for discovering, normalizing, reconciling, and managing infrastructure and application resources across multiple environments.

## Vision

Open CMDB aims to provide an extensible, developer-friendly alternative for building a modern Configuration Management Database (CMDB).

The platform is designed around a connector-driven architecture where external systems can be discovered through reusable connectors, transformed through configurable pipelines, and loaded into a common CMDB data model.

The long-term goal is to provide:

* Multi-cloud and infrastructure discovery
* Extensible connector architecture
* Pluggable authentication mechanisms
* Configurable ETL pipelines
* Resource identification and reconciliation
* Relationship and dependency modeling
* Service-oriented CMDB views
* Web-based CMDB exploration
* Graph-based dependency and impact analysis
* AI-assisted CMDB queries and automation

## Core Principles

### 1. Connector-first architecture

External systems should be integrated through well-defined connectors rather than tightly coupled application code.

Connectors should be able to support:

* Authentication
* Resource discovery
* Pagination
* Retries and rate limiting
* Incremental synchronization
* Resource normalization
* Relationship discovery

### 2. Common resource model

Resources discovered from different systems should be normalized into a common CMDB model while preserving provider-specific attributes and source information.

### 3. Declarative data pipelines

Ingestion and transformation workflows should be configurable and reproducible rather than requiring custom code for every integration.

### 4. Identification and reconciliation

The platform should determine whether an incoming resource already exists and reconcile conflicting attributes from multiple sources according to explicit rules.

### 5. Extensibility

New connectors, authentication providers, resource types, pipeline stages, and relationship types should be possible without modifying the core platform unnecessarily.

### 6. API-first design

Core CMDB functionality should be exposed through well-defined APIs so that the platform can support multiple clients and automation workflows.

### 7. Local-first development

The complete development environment should be reproducible locally using containerized infrastructure wherever practical.

## Planned Architecture

At a high level, the platform will consist of:

```text
                    External Systems
                           |
             +-------------+-------------+
             |             |             |
          GCP           AWS          Kubernetes
             |             |             |
             +-------------+-------------+
                           |
                    Connector SDK
                           |
                  Authentication
                           |
                    Discovery Layer
                           |
                  Normalization
                           |
                    ETL Pipelines
                           |
              +------------+------------+
              |                         |
       Identification              Validation
              |                         |
              +------------+------------+
                           |
                     Reconciliation
                           |
                         CMDB
                           |
             +-------------+-------------+
             |             |             |
        REST API       Service Model    Graph
             |             |             |
             +-------------+-------------+
                           |
                       React UI
                           |
                     AI Layer
```

This architecture is intentionally high-level and will evolve as implementation progresses.

## Major Components

### Backend

The backend will provide:

* CMDB APIs
* Resource management
* Relationship management
* Connector execution
* Pipeline execution
* Identification and reconciliation
* Service model APIs

### Connector SDK

The connector SDK will provide common abstractions for:

* Connectors
* Resources
* Authentication
* HTTP clients
* Pagination
* Retries
* Connector configuration
* Connector lifecycle

### ETL Engine

The ETL engine will provide configurable stages for:

```text
Extract → Transform → Map → Validate → Load
```

### CMDB

The CMDB will maintain:

* Resource types
* Resources
* Attributes
* Sources
* Relationships
* Relationship types
* Identification metadata
* Reconciliation metadata

### Service Model

The service model will provide higher-level representations such as:

* Organizations
* Business capabilities
* Business services
* Applications
* Application services
* Infrastructure services
* Infrastructure CIs

### Frontend

A React-based interface will eventually provide:

* Dashboard
* Connector management
* Pipeline management
* Execution history
* CMDB explorer
* Resource details
* Relationship visualization
* Service model views

### AI Layer

The AI layer will eventually support:

* Natural-language CMDB queries
* Dependency explanations
* Ownership and data-quality queries
* Change analysis
* Connector generation assistance
* Pipeline generation assistance

AI functionality will operate within explicit permission and safety boundaries.

## Initial Development Milestones

The project will be developed incrementally.

### Phase 0 — Repository & Project Foundation

Establish the repository structure, development conventions, Docker foundation, documentation, and architecture.

### Phase 1 — Local Infrastructure

Create the local PostgreSQL, Redis, and MinIO development environment.

### Phase 2 — Backend Foundation

Establish the Python backend, FastAPI, SQLAlchemy, PostgreSQL connectivity, migrations, configuration, logging, and testing.

### Phase 3 — First API

Create the first working API with health checks, validation, error handling, OpenAPI documentation, and integration tests.

Subsequent phases will progressively introduce the CMDB data model, connector SDK, mock connector, execution engine, real cloud connectors, authentication framework, ETL, identification, reconciliation, relationships, service modeling, UI, graph capabilities, connector marketplace, and AI.

## Technology Direction

The initial implementation is expected to use:

* **Backend:** Python + FastAPI
* **Database:** PostgreSQL
* **Cache / coordination:** Redis
* **Object storage:** MinIO
* **Frontend:** React + TypeScript + Vite
* **Containerization:** Docker Compose
* **ORM:** SQLAlchemy
* **Database migrations:** Alembic

Technology choices may evolve as the architecture is validated.

## Project Status

This project is under active development.

The master build roadmap tracks implementation at the task level. Work should proceed sequentially, with each major phase producing a locally verified result before moving to the next phase.

## License

License information will be added in a subsequent project-foundation task.

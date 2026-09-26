# Open CMDB Architecture

## 1. Overview

Open CMDB is an open-source Integration, ETL, CMDB, and Service Model platform.

The platform discovers resources from external systems through connectors, normalizes the discovered data, processes it through configurable pipelines, identifies and reconciles resources, stores the resulting configuration data, and exposes the information through APIs and a web UI.

The architecture is designed around the following principles:

- Connector-driven integrations
- Pluggable authentication
- Separation of discovery, transformation, and persistence
- Configurable ETL pipelines
- Resource identification and reconciliation
- Extensible relationship modeling
- API-first backend
- Asynchronous execution for long-running operations
- Technology-independent CMDB data model
- Local-first development using Docker Compose

---

## 2. High-Level Architecture

```text
                         External Systems
                              │
              ┌───────────────┼────────────────┐
              │               │                │
          AWS APIs        GCP APIs        Other APIs
              │               │                │
              └───────────────┼────────────────┘
                              │
                       Connector SDK
                              │
                    ┌─────────▼─────────┐
                    │  Authentication   │
                    │      Layer        │
                    └─────────┬─────────┘
                              │
                    ┌─────────▼─────────┐
                    │ Discovery / Fetch │
                    │    Execution      │
                    └─────────┬─────────┘
                              │
                    ┌─────────▼─────────┐
                    │    Raw Resource   │
                    │       Data        │
                    └─────────┬─────────┘
                              │
                    ┌─────────▼─────────┐
                    │   ETL Pipeline    │
                    │                   │
                    │ Normalize         │
                    │ Transform         │
                    │ Enrich            │
                    │ Validate          │
                    └─────────┬─────────┘
                              │
                    ┌─────────▼─────────┐
                    │ Identification &  │
                    │  Reconciliation   │
                    └─────────┬─────────┘
                              │
                    ┌─────────▼─────────┐
                    │       CMDB        │
                    │                   │
                    │ Resources         │
                    │ Relationships     │
                    │ Services          │
                    └─────────┬─────────┘
                              │
                ┌─────────────┼──────────────┐
                │             │              │
                ▼             ▼              ▼
             REST API      Web UI        Graph / AI

---

## 3. Core Components

### 3.1 Backend API

The backend is responsible for:

REST APIs
Authentication and authorization
CMDB operations
Connector management
Pipeline management
Execution management
Resource queries
Relationship queries
Health and operational endpoints

### Technology:

- Python
- FastAPI
- SQLAlchemy
- Alembic

---

## 3.2 Connector SDK

The Connector SDK provides a common interface for integrations.

A connector is responsible for communicating with an external system and retrieving resources.

### Examples:

- GCP Connector
- AWS Connector
- Azure Connector
- Kubernetes Connector
- GitHub Connector

---

Connectors should not directly implement CMDB persistence.

Instead, they return normalized or raw resource data to the execution pipeline.

Conceptually:

```
Connector
   │
   ├── Authentication
   ├── Resource Discovery
   ├── Pagination
   ├── Rate Limiting
   └── Retry Handling
```

## 3.3 Authentication Layer

Authentication is separated from connector logic.

The authentication layer should support multiple authentication mechanisms.

### Initial and planned mechanisms include:

- API Key
- Basic Authentication
- OAuth 2.0
- OIDC
- AWS IAM
- GCP Service Account
- Workload Identity
- AWS STS / AssumeRole

The goal is to allow connectors to request credentials without needing to know how those credentials are obtained or stored.

---

## 3.4 Execution Engine

The execution engine coordinates connector runs.

### Responsibilities include:

- Starting connector executions
- Managing execution state
- Handling pagination
- Retry handling
- Rate limiting
- Logging
- Error handling
- Tracking execution metrics
- Passing discovered data into pipelines

Long-running operations should eventually execute asynchronously.

---

## 3.5 ETL Pipeline

The ETL layer processes discovered resources.

### The conceptual flow is:

```
Raw Data
   │
   ▼
Parse
   │
   ▼
Normalize
   │
   ▼
Transform
   │
   ▼
Enrich
   │
   ▼
Validate
   │
   ▼
Identify
   │
   ▼
Reconcile
   │
   ▼
CMDB
```
Pipelines should be configurable and composable.

---
## 3.6 CMDB Data Model

The CMDB stores normalized configuration information.

### Core concepts include:

- Resource
- Resource Type
- Attribute
- Relationship
- Relationship Type
- Source
- Service
- Environment
- Tenant

A resource represents an entity discovered from an external system.

### Examples:

```
GCP VM
AWS EC2 Instance
Kubernetes Pod
Database
Load Balancer
Application
Service
```

---

## 3.7 Identification and Reconciliation

Multiple sources may describe the same real-world resource.

The identification layer determines whether an incoming resource already exists.

### Conceptually:

```
Incoming Resource
       │
       ▼
Identity Rules
       │
       ├── Existing Resource ──► Update
       │
       └── New Resource ───────► Create
```
Reconciliation determines which source should be authoritative for individual attributes.

---

## 3.8 Relationship Engine

Resources are connected through relationships.

### Examples:

```
Application
    │
    └── runs_on ──► Kubernetes Pod
                         │
                         └── runs_on ──► Node

Service
    │
    └── depends_on ──► Database
```
The relationship engine will eventually support:

- Dependency relationships
- Parent/child relationships
- Hosting relationships
- Network relationships
- Service relationships
- User-defined relationships

---

## 4. Data Storage

The initial architecture uses:

### PostgreSQL

Primary persistent datastore.

Used for:

- CMDB resources
- Relationships
- Connector configuration
- Execution metadata
- Pipeline configuration
- Authentication metadata
- Service model

### Redis

Used for:

- Caching
- Distributed coordination
- Job state
- Rate limiting
- Future asynchronous execution

### MinIO

Used for object storage.

Potential uses:

- Raw discovery payloads
- Large connector responses
- Pipeline artifacts
- Execution artifacts
- Export files

---

## 5. Application Structure

The repository is organized into separate areas:

```
backend/
    API and application services

frontend/
    React web application

connectors/
    Connector implementations

pipelines/
    ETL pipeline implementations

infrastructure/
    Docker and infrastructure configuration

docs/
    Architecture and developer documentation

tests/
    Automated tests

scripts/
    Developer and operational scripts
```

---

## 6. Request Flow
## API Request

```
Client
  │
  ▼
FastAPI
  │
  ▼
API Router
  │
  ▼
Service Layer
  │
  ▼
Repository / Data Access
  │
  ▼
PostgreSQL
```

---

## 7. Discovery Flow

A typical discovery execution will eventually follow this flow:

```
User / Scheduler
       │
       ▼
Execution API
       │
       ▼
Execution Engine
       │
       ▼
Connector
       │
       ▼
Authentication Provider
       │
       ▼
External API
       │
       ▼
Pagination / Retry / Rate Limit
       │
       ▼
Raw Resources
       │
       ▼
ETL Pipeline
       │
       ▼
Identification
       │
       ▼
Reconciliation
       │
       ▼
CMDB
```

---

## 8. Asynchronous Execution

Connector discovery and ETL processing may involve large amounts of data and long-running operations.

Therefore, the architecture should separate:

```
Request submission
```
from:
```
Execution processing
```
A future execution model will be:
```
POST /executions
       │
       ▼
Create Execution
       │
       ▼
Queue Job
       │
       ▼
Worker
       │
       ├── Connector
       ├── ETL
       ├── Identification
       └── Reconciliation
       │
       ▼
Update Execution Status
```
The initial implementation does not need a distributed worker system. The architecture should allow one to be introduced without redesigning the core domain model.

---

## 9. Configuration and Secrets

Configuration should be separated from source code.

Environment variables will be used for local development.

Example:
```
DATABASE_URL
REDIS_URL
MINIO_ENDPOINT
MINIO_ACCESS_KEY
MINIO_SECRET_KEY
```
Secrets must not be committed to Git.

Production deployments should eventually support external secret-management systems.

---

## 10. API Design

The backend follows an API-first approach.

Initial API groups will eventually include:
```
/health

/resources
/resource-types

/connectors
/connector-types

/executions

/pipelines

/relationships

/services
```
API versioning will use:
```
/api/v1
```

---

## 11. Frontend Architecture

The frontend will use:

- React
- TypeScript
- Vite

The frontend communicates with the backend exclusively through APIs.

Conceptually:
```
React UI
   │
   ▼
REST API
   │
   ▼
FastAPI
```
The frontend should not directly access PostgreSQL, Redis, MinIO, or external cloud APIs.

---

## 12. Extensibility

The architecture should allow new capabilities to be added without modifying the core CMDB engine.

Examples:
- New Connector
- New Authentication Provider
- New Pipeline Stage
- New Resource Type
- New Relationship Type
- New Identification Rule
- New Reconciliation Strategy
- New UI Module
This is one of the primary architectural goals of Open CMDB.

---

## 13. Initial Technology Stack

| Component | Technology |
|-----------|------------|
| Backend | Python + FastAPI |
| ORM | SQLAlchemy |
| Database migrations | Alembic |
| Database | PostgreSQL |
| Cache / coordination | Redis |
| Object storage | MinIO |
| Frontend | React + TypeScript + Vite |
| Containers | Docker |
| Local orchestration | Docker Compose |
| API style | REST |
| API documentation | OpenAPI |

---

## 14. Architecture Evolution

The architecture will evolve incrementally.

The initial implementation will prioritize:
1. Local development environment
2. Backend foundation
3. CMDB data model
4. Connector SDK
5. Connector execution
6. ETL
7. Identification and reconciliation
8. Relationships
9. Service model
10. Web UI
11. Graph capabilities
12. AI capabilities

Components should only become distributed or operationally complex when there is a demonstrated need.

# ADR-002: Persistence Strategy

## Status

Accepted

## Context

TCGBase requires a persistence strategy capable of supporting the MVP domain, transactional consistency, maintainability, automated migrations, integration testing and future scalability without introducing unnecessary operational complexity.

The current MVP represents a single platform used by multiple users.

Users may create and manage their own collections, while catalog data such as TCGs, sets and cards is shared across the platform.

The domain contains strongly related entities such as:

- Users
- TCGs
- Sets
- Cards
- Collection Folders
- Collection Cards
- Card Requests
- Card Issues

Because these entities have clear relationships and consistency requirements, the persistence model should favor relational integrity and transactional operations.

The persistence strategy must also avoid introducing technologies only for architectural experimentation when no current business requirement justifies them.

---

## Requirements

The persistence solution should:

- Support a relational domain model
- Provide ACID transactional consistency
- Support relationships, constraints and indexes
- Integrate well with .NET and Entity Framework Core
- Support schema versioning through migrations
- Work well in containerized local development
- Support automated integration testing
- Be suitable for future cloud deployment
- Minimize licensing and operational complexity
- Allow future evolution if business requirements change

---

## Options Considered

### PostgreSQL

#### Advantages

- Mature relational database
- Strong ACID transaction support
- Excellent support for constraints, indexes and relational modeling
- Strong integration with Entity Framework Core through Npgsql
- Open-source licensing
- Lightweight and straightforward containerized development
- Strong support across major cloud providers
- Native support for JSON and JSONB when semi-structured data is required
- Good support for advanced indexing and query capabilities

#### Disadvantages

- Requires migration and schema management
- Introduces a database technology that may require additional learning for developers more familiar with SQL Server
- PostgreSQL-specific features may reduce database portability if heavily used

---

### SQL Server

#### Advantages

- Mature relational database
- Excellent ACID transaction support
- Excellent integration with .NET and Entity Framework Core
- Strong tooling and ecosystem support
- Mature indexing, constraints and query optimization capabilities
- Widely used in enterprise .NET environments

#### Disadvantages

- Licensing may introduce additional cost depending on deployment model and edition
- Local and cloud deployment may carry higher operational or licensing considerations than PostgreSQL
- For the current project, it does not provide a significant functional advantage over PostgreSQL

#### Evaluation

SQL Server is considered a fully valid technical option for TCGBase.

It would satisfy the functional and architectural requirements of the MVP.

PostgreSQL is preferred primarily because it provides equivalent relational capabilities for the current requirements while offering open-source licensing, simple containerized development and strong portability across cloud environments.

---

### Oracle Database

#### Advantages

- Mature enterprise relational database
- Strong transactional consistency
- Advanced enterprise features
- Proven scalability for large and complex workloads

#### Disadvantages

- Higher licensing and operational complexity
- Heavier development and infrastructure requirements
- More complex local development environment
- The current TCGBase requirements do not depend on Oracle-specific capabilities
- Adds unnecessary complexity for an MVP-sized application

#### Evaluation

Oracle was rejected for the MVP.

Although technically capable of supporting TCGBase, its additional operational and licensing complexity is not justified by any current business or technical requirement.

---

### MongoDB

#### Advantages

- Flexible document-oriented model
- Useful for highly dynamic or schema-flexible data
- Good support for document-centric use cases

#### Disadvantages

- The current TCGBase domain is strongly relational
- Relationships between cards, sets, collections and users are central to the domain
- Relational consistency would require additional application-level handling
- Would introduce another persistence paradigm without a clear business requirement

#### Evaluation

MongoDB was rejected as the primary persistence database for the MVP.

It may be reconsidered in the future if a document-oriented use case is identified that provides a clear advantage over the relational model.

---

## Decision

TCGBase will use **PostgreSQL** as its primary persistence database.

Entity Framework Core will be used as the primary data access technology through the Npgsql provider.

Persistence-related implementations will reside in the `TCGBase.Infrastructure` project.

The `Domain` and `Application` projects must not depend directly on PostgreSQL-specific infrastructure.

Entity Framework Core dependencies should remain outside the Domain and Application layer.

---

## Entity Framework Core Strategy

Entity Framework Core will provide:

- DbContext
- Entity mappings
- Relationships
- Constraints
- Migrations
- Change tracking
- Transaction management
- Query composition

The persistence model will be configured primarily through Fluent API mappings inside the Infrastructure layer.

Generic repositories will not be introduced by default.

Repository abstractions may be added when they represent a meaningful domain or application boundary rather than simply wrapping `DbSet<T>` operations.

This avoids unnecessary abstraction while preserving the ability to introduce specific repositories where they provide value.

---

## Multi-Tenancy Decision

Multi-tenancy was evaluated but is **deferred for the MVP**.

The current TCGBase business model represents:

- One platform
- Multiple users
- Shared catalog data
- User-owned collections

This is a multi-user system, but not currently a multi-tenant system.

A user does not represent a tenant.

Introducing tenant identifiers, tenant resolution, tenant-aware query filters or tenant-specific databases without an actual business requirement would add unnecessary complexity.

For the MVP, ownership and authorization boundaries will be based on the authenticated user where appropriate.

For example:

```text
User
  └── Collection Folder
        └── Collection Cards
```

The system may evolve to multi-tenancy in the future if TCGBase introduces independent organizational contexts such as:

- TCG stores
- Clubs
- Leagues
- Communities
- Organizations
- Workspaces

In that scenario, a tenant could represent one of these organizational boundaries.

Possible future multi-tenancy strategies include:

- Shared database and shared schema with `TenantId`
- Shared database with schema per tenant
- Database per tenant
- Hybrid approaches

A separate ADR must be created before multi-tenancy is introduced.

---

## Additional Persistence Technologies

### Redis

Redis is deferred.

No current MVP requirement justifies distributed caching or distributed state.

Redis may be introduced if a measurable need is identified, such as:

- Expensive or frequently repeated catalog queries
- Distributed caching
- Distributed rate limiting
- Distributed state
- Performance requirements not efficiently addressed by PostgreSQL

Redis must not be introduced solely to increase the number of technologies used by the project.

---

### MongoDB

MongoDB is deferred.

The current relational model is sufficient for the identified domain requirements.

MongoDB may be reconsidered if future features introduce document-oriented data that is difficult or inefficient to model relationally.

---

## Consequences

### Positive

- Persistence architecture remains simple during the MVP
- Strong relational consistency
- Excellent integration with the .NET ecosystem
- Low licensing cost
- Straightforward containerized development
- Suitable for automated testing using Testcontainers
- Clear responsibility boundaries between Domain, Application and Infrastructure
- Avoids premature multi-tenancy complexity
- Avoids unnecessary polyglot persistence

### Negative

- PostgreSQL introduces additional learning requirements for developers primarily experienced with SQL Server
- Schema evolution must be managed through migrations
- Some PostgreSQL-specific features may create database coupling
- Future introduction of multi-tenancy may require changes to the persistence model
- Redis or another specialized storage mechanism may eventually be required as the system grows

---

## Future Evolution

Possible future changes include:

- Multi-tenancy
- Redis-based caching
- Read replicas
- Database partitioning
- PostgreSQL JSONB for appropriate semi-structured data
- Specialized document persistence
- Dedicated search infrastructure
- Database-per-tenant strategies

Any additional persistence technology must solve an identified business or technical requirement before being introduced.

---

## Guiding Principle

TCGBase should prefer the simplest persistence architecture that satisfies current requirements while preserving a reasonable evolution path.

Technology should be introduced to solve identified problems, not solely to demonstrate technical breadth.
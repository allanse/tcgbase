# ADR-001: Architecture Baseline

## Status

Accepted

## Context

TCGBase is a multi-tenant Trading Card Game collection platform.

The application is expected to evolve over time and include multiple business capabilities, such as authentication, user management, TCG and card catalog management, collection management, and administration.

The architecture should provide:

* Clear separation of responsibilities
* Maintainability
* Testability
* Controlled dependency flow
* Isolation between business rules and infrastructure concerns
* Room for future evolution without introducing unnecessary complexity

## Decision

TCGBase will adopt **Clean Architecture** as its main structural architecture.

Within the Application layer, features will be organized using **Vertical Slice Architecture principles**, grouping code by business capability rather than only by technical type.

**CQRS principles** will be applied when separating read and write operations provides a clear benefit. CQRS will not be used mechanically for every use case.

The initial solution will contain the following projects:

```text
src/
├── TCGBase.Api
├── TCGBase.Application
├── TCGBase.Domain
└── TCGBase.Infrastructure

tests/
├── TCGBase.UnitTests
└── TCGBase.IntegrationTests
```

## Application Organization

Application features should be organized primarily by business capability.

Example:

```text
TCGBase.Application/
├── Authentication/
│   ├── Login/
│   └── Register/
│
├── Collections/
│   ├── CreateCollection/
│   ├── AddCard/
│   └── RemoveCard/
│
├── Cards/
│   ├── SearchCards/
│   └── ReportCardIssue/
│
└── Administration/
    ├── ManageUsers/
    └── ManageCards/
```

The internal structure of each feature may evolve as implementation needs become clearer.

## Dependency Rules

The following dependency rules will be adopted:

* `TCGBase.Domain` must not depend on any other TCGBase project.
* `TCGBase.Application` may depend on `TCGBase.Domain`.
* `TCGBase.Infrastructure` may depend on `TCGBase.Application` and `TCGBase.Domain`.
* `TCGBase.Api` may depend on `TCGBase.Application` and `TCGBase.Infrastructure` for application composition and dependency injection.
* Business rules must not depend directly on ASP.NET Core, Entity Framework Core, PostgreSQL, Redis, messaging brokers, or external services.

The expected dependency direction is:

```text
TCGBase.Api
    │
    ▼
TCGBase.Application
    │
    ▼
TCGBase.Domain

TCGBase.Infrastructure
    │
    ├──► TCGBase.Application
    └──► TCGBase.Domain
```

## CQRS Guidelines

CQRS will be used as an application design principle, not as a requirement for every operation.

Commands represent operations that change application state.

Examples:

```text
CreateCollection
AddCardToCollection
BlockUser
ReportCardIssue
```

Queries represent read-only operations.

Examples:

```text
GetCollection
SearchCards
GetUserDetails
```

Commands and queries may use different models or persistence strategies when doing so provides a clear technical or business benefit.

## Consequences

### Positive

* Business rules remain isolated from infrastructure concerns.
* Domain and application logic can be tested independently.
* Features are grouped around business capabilities.
* Changes within one feature should have reduced impact on unrelated features.
* Infrastructure technologies can evolve without directly affecting the domain.
* The project provides practical experience with Clean Architecture, Vertical Slice Architecture, and CQRS.

### Negative

* The solution contains more projects and architectural boundaries than a simple layered application.
* Vertical Slice and CQRS patterns may increase the number of classes and files.
* Developers must understand and respect dependency rules.
* Overusing abstractions or patterns could introduce unnecessary complexity.

## Alternatives Considered

### Clean Architecture without Vertical Slice organization

This option provides strong dependency boundaries but can lead to Application code being organized primarily by technical concerns, such as services, repositories, validators, and handlers.

### Vertical Slice Architecture without explicit Clean Architecture boundaries

This option provides strong feature-oriented organization but offers less explicit separation between domain, application, infrastructure, and delivery concerns.

### Traditional Layered Architecture

This option would be simpler initially, but it could create stronger coupling between business rules and infrastructure as the application grows.

## Guiding Principle

Architectural patterns and abstractions must only be introduced when they solve an identified problem or provide a clear technical or business benefit.

Complexity must be justified.

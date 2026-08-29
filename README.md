# TCGBase

**Multi-tenant Trading Card Game Collection Platform**

TCGBase is a portfolio and learning project focused on building a production-oriented platform for managing Trading Card Game collections.

## Goals

The project is designed to explore and demonstrate modern software engineering practices with C# and .NET, including:

- Clean Architecture
- Domain-driven design principles
- Multi-tenancy
- ASP.NET Core Web API
- Entity Framework Core
- PostgreSQL
- Automated testing and integration testing with Testcontainers
- Authentication and authorization
- Caching and asynchronous messaging
- Observability
- Docker
- GitHub Actions CI/CD
- Infrastructure as Code
- Google Cloud deployment

## MVP

The first version is divided into two main areas:

### Administration

Key Users will be able to:

- Manage TCGs
- Manage card sets and cards
- Manage users, including blocking and unblocking accounts
- Review card registration requests
- Review reported card issues

### Players / Collectors

Users will be able to:

- Create, edit and delete collection folders
- Select the TCG associated with a folder
- Add and remove cards from their collections
- Manage card quantities
- Report problems with existing card records
- Request missing cards to be added to the platform

## Architecture

Architecture decisions will be documented using Architecture Decision Records (ADRs) under `docs/adr`.

The implementation will evolve incrementally. Technologies such as Redis, RabbitMQ, OpenTelemetry and Google Cloud services will be introduced when there is a clear architectural or business reason to use them.

## Repository Structure

```text
src/        Application source code
tests/      Automated tests
docs/       Architecture and project documentation
.github/    GitHub configuration and workflows
```

## Status

🚧 Early planning / Foundation

The project is being developed incrementally as a learning and portfolio project.

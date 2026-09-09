# Local Database

TCGBase uses PostgreSQL as its primary relational database.

The local development database runs through Docker Compose.

## Requirements

- Docker Desktop
- Docker Compose

## Configuration

Create a local `.env` file based on `.env.example`.

Example:

```env
POSTGRES_DB=tcgbase
POSTGRES_USER=tcgbase
POSTGRES_PASSWORD=tcgbase_dev_password
POSTGRES_PORT=5432
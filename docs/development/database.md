# Local Database

TCGBase uses PostgreSQL as its primary relational database.

The local development database runs through Docker Compose.

## Requirements

- Docker Desktop
- Docker Compose

Make sure Docker Desktop is running before starting the database.

## Configuration

Create a local `.env` file based on `.env.example`.

Example:

```env
POSTGRES_DB=tcgbase
POSTGRES_USER=tcgbase
POSTGRES_PASSWORD=tcgbase_dev_password
POSTGRES_PORT=5432
```

The `.env` file contains local development configuration and must not be committed to the repository.

The `.env.example` file documents the environment variables required to run the database locally.

## Start PostgreSQL

```bash
docker compose up -d
```

The `-d` option starts the container in detached mode.

## Check Container Status

```bash
docker compose ps
```

The PostgreSQL container should eventually report a `healthy` status.

Example:

```text
NAME               STATUS
tcgbase-postgres   Up (healthy)
```

## Validate Database Connectivity

Connect to PostgreSQL directly from the container:

```bash
docker exec -it tcgbase-postgres psql -U tcgbase -d tcgbase
```

After connecting, run:

```sql
SELECT version();
```

Exit `psql` with:

```text
\q
```

## View Logs

```bash
docker compose logs postgres
```

To follow the logs continuously:

```bash
docker compose logs -f postgres
```

## Stop PostgreSQL

```bash
docker compose down
```

The PostgreSQL data is preserved because it is stored in a Docker volume.

## Persistent Storage

PostgreSQL data is stored in the Docker volume defined in `docker-compose.yml`:

```text
postgres_data
```

The volume preserves database data when the container is stopped or removed.

## Reset the Local Database

To stop PostgreSQL and remove the persisted database data:

```bash
docker compose down -v
```

> **Warning:** This removes the PostgreSQL Docker volume and permanently deletes the local database data.

Run the following command to create a new database environment:

```bash
docker compose up -d
```

## Troubleshooting

### Docker Desktop Is Not Running

If Docker Compose cannot connect to the Docker engine, make sure Docker Desktop is running.

Validate the Docker environment with:

```bash
docker version
```

or:

```bash
docker ps
```

Then retry:

```bash
docker compose up -d
```

### Port Already in Use

If port `5432` is already being used, change `POSTGRES_PORT` in the local `.env` file.

Example:

```env
POSTGRES_PORT=5433
```

Restart the environment:

```bash
docker compose down
docker compose up -d
```

## Related Documentation

The decision to use PostgreSQL as the primary persistence database is documented in:

- `docs/adr/ADR-002-persistence-strategy.md`
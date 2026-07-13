# Simple SQL Server Docker Compose

A lightweight Docker Compose setup to run SQL Server 2019 with the AdventureWorks sample database.

## What This Does

- Spins up a **SQL Server 2019** container using Docker
- Provides a persistent volume for database data
- Mounts the `backup/` folder so you can restore database backups (e.g., AdventureWorks2019.bak)

## Prerequisites

- [Docker](https://www.docker.com/products/docker-desktop)
- [Docker Compose](https://docs.docker.com/compose/install/)

## Quick Start

### 1. Start the Container

```bash
docker-compose up -d
```

The SQL Server container will start and be accessible at `localhost:1433`.

### 2. Restore the AdventureWorks Database

Connect to the container using SQL Server Management Studio (SSMS) or `sqlcmd`:

**Connection Details:**
- **Server**: `localhost`
- **User**: `sa`
- **Password**: `1234`

Then restore the backup:

```sql
RESTORE DATABASE AdventureWorks2019
FROM DISK = '/var/opt/mssql/backup/AdventureWorks2019.bak'
WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf',
     MOVE 'AdventureWorks2017_log' TO '/var/opt/mssql/data/AdventureWorks2019_log.ldf'
```

Or using `sqlcmd` from the container:

```bash
docker exec -it sqlserver /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P "1234" -Q "RESTORE DATABASE AdventureWorks2019 FROM DISK = '/var/opt/mssql/backup/AdventureWorks2019.bak'"
```

### 3. Verify the Database is Running

```bash
docker ps
```

You should see the `sqlserver` container running.

## Useful Commands

Stop the container:
```bash
docker-compose down
```

View logs:
```bash
docker-compose logs -f mssql
```

Connect to the container terminal:
```bash
docker exec -it sqlserver /bin/bash
```

## File Structure

```
DataBaseUp/
├── docker-compose.yaml    # Docker configuration
├── backup/
│   └── AdventureWorks2019.bak  # Database backup (not tracked by git)
└── README.md              # This file
```

## Notes

- The `.gitignore` file excludes large backup files from version control
- Database data persists in the `mssql_data` Docker volume
- ⚠️ Change the `SA_PASSWORD` in `docker-compose.yaml` for production use (do not use weak passwords like "1234")

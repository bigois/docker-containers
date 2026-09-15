# docker-containers

This repository contains Docker Compose configurations for local development services. Each directory is self-contained, so you can start only the database, tool, or integration service you need.

It is not a single application. It is a practical collection of reusable local containers for development and study environments.

## Purpose

Serve as a personal local-infrastructure reference that can grow with new Docker services, configurations, and development experiments.

## Stack

* Docker and Docker Compose
* PostgreSQL 15
* MySQL 8.0
* MongoDB
* Microsoft SQL Server 2022 Developer
* Apache NiFi
* Open WebUI with Ollama access
* TOTVS DBAccess

## Structure

```text
.
├── dbaccess/       # TOTVS DBAccess image build and Compose configuration
├── mongodb/        # MongoDB service
├── mssql/          # SQL Server service
├── mysql/          # MySQL service
├── nifi/           # Apache NiFi service and persistent directories
├── open-webui/     # Open WebUI connected to Ollama on the host
└── postgres/       # PostgreSQL service
```

Each service stores its local data under its own `data` directory. These directories are ignored by Git to keep generated data out of the repository.

## Running locally

### Prerequisites

* Docker Desktop running with Docker Compose available.
* An `APP_SECRET` environment variable. The Compose files stop with an error when it is not set.

Set it permanently for your Windows user:

```powershell
[Environment]::SetEnvironmentVariable("APP_SECRET", "your-strong-secret", "User")
```

Restart Docker Desktop and open a new terminal after setting it. For the current PowerShell session only:

```powershell
$env:APP_SECRET = "your-strong-secret"
```

The same value is passed to the services that require a password or application secret. It must meet the password requirements of every service you plan to run; SQL Server requires a strong password with uppercase, lowercase, number, and symbol characters.

### Start a service

Run the following command from the repository root, replacing `<service>` with the target directory:

```powershell
docker compose -f .\<service>\docker-compose.yml up -d
```

For example, to start PostgreSQL:

```powershell
docker compose -f .\postgres\docker-compose.yml up -d
```

Build and start DBAccess from its own directory because it uses the local `.env` file and Dockerfile:

```powershell
Set-Location .\dbaccess
docker compose up -d --build
```

### Services

| Service | Port | Notes |
| --- | ---: | --- |
| PostgreSQL | 5432 | Uses the `postgres` user. |
| MySQL | 3306 | Creates the `master` database. |
| MongoDB | 27017 | Creates the configured root user on first initialization. |
| SQL Server | 1433 | Runs the Developer edition. |
| Apache NiFi | 8443 | Uses the configured single-user credentials. |
| Open WebUI | 3000 | Connects to Ollama at `host.docker.internal:11434`. |
| DBAccess | 7890 | Built locally from the `dbaccess` directory. |

## Setting up Core Services(MySql, Redis, Nats, ClickHouse, PostgreSql)
This section covers the **initialization of Core services** required by the system before the main application components are deployed. These services provide the core infrastructure layer responsible for data storage, caching, messaging, and analytics, and must be configured and running correctly to ensure overall system stability and performance.

The initialization process is managed using a dedicated Docker Compose configuration file:

```
docker-compose-init.yml
```

Using this compose file ensures that all foundational services are started in a controlled and consistent manner, allowing the application stack to connect to reliable dependencies from the beginning.

The services initialized in this phase include:

- **MySQL** — Primary relational database for transactional data.
- **Redis** — In-memory data store used for caching and fast data access.
- **NATS** — Lightweight messaging system for event-driven communication.
- **ClickHouse** — High-performance analytical database for large-scale data processing.
- **PostgreSQL (TimescaleDB)** — Time-series database component requiring manual setup and configuration.

By completing this initialization phase first, the environment is prepared to support application services, background jobs, and analytics workflows in a predictable and maintainable way.

## Offline Initialization of Third-Party Services Using Docker

This guide explains how to prepare and run required third-party services on a Linux server **without internet access**. Docker images are downloaded on a Windows machine with internet connectivity, transferred to the Linux server, and then loaded and started offline.

---

## PART 1 — Prepare Docker Images on Windows (Internet Machine)

On your **Windows** machine:

### Pull Required Docker Images

Open terminal (PowerShell / CMD) and run:

```bash
docker pull redis:latest
docker pull nats:latest
docker pull mysql:latest
docker pull clickhouse/clickhouse-server:latest
```

---

###  Export Images (if not already exported)

Save images as `.tar` files for offline transfer:

```bash
docker save -o redis_latest.tar redis:latest
docker save -o nats_latest.tar nats:latest
docker save -o mysql_latest.tar mysql:latest
docker save -o clickhouse_latest.tar clickhouse/clickhouse-server:latest
```

---

### Copy Files to Linux Server

Using **WinSCP**, transfer the following files:

```
redis_latest.tar
nats_latest.tar
mysql_latest.tar
clickhouse_latest.tar
docker-compose-init.yml
mysql_create_table_schema.txt
mysql/initdb/
clickhouse-init/
clickhouse-config/
docker-compose-init.yml
```

Target directory on Linux:

```
/opt/app/
```

---

## PART 2 — Load Docker Images on Linux (Offline Server)

Login to Linux using **PuTTY** and navigate to the directory:

```bash
cd /opt/app
```

### Load Docker Images

```bash
docker load -i redis_latest.tar
docker load -i nats_latest.tar
docker load -i mysql_latest.tar
docker load -i clickhouse_latest.tar
```

---

### Verify Loaded Images

```bash
docker images
```

You should see:

```
redis
nats
mysql
clickhouse/clickhouse-server
```

---

## PART 3 — Run Docker Compose Offline

###  Verify Docker Installation

```bash
docker --version
docker-compose --version
```

---

###  Start Services

```bash
docker-compose -f docker-compose-init.yml up -d
```

If containers already exist:

```bash
docker-compose down
docker-compose -f docker-compose-init.yml up -d
```

---

## PART 4 — Verify Running Containers

### Check Container Status

```bash
docker ps
```

Expected running containers:

```
blitz_redis
blitz_nats
blitz_mysql
blitz_clickhouse
```

---


## Summary

This process enables a complete **offline initialization** of infrastructure services using Docker:

- Redis (Caching)
- NATS (Messaging)
- MySQL (Primary Database)
- ClickHouse (Analytics Database)

By preparing images on an internet-connected system and deploying them offline, you ensure repeatable, secure, and controlled environment setup suitable for restricted or production-grade infrastructure.

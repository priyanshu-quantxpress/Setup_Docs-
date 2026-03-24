### MySQL Initial Setup

!!! info
    This section covers the **initial MySQL configuration** required to prepare the **Blitz database** after the MySQL container is up and running.

These steps ensure that the database service is accessible and that all required tables are created correctly before proceeding with application setup.

---

### 🔐 Step 1 — Access the MySQL Container

Connect to the running MySQL container using Docker:

```bash
docker exec -it blitz_mysql mysql -u root -p
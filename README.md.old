# Open Fabric Studio — Deployment Guide (v0.8.5)

Real-time visual network planning, monitoring, and configuration for professional AV networks.

---

## Currently supported Network Hardware

- Catalyst 9000
- Nexus 9300

  ---

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/) (version 24 or later)
- Docker Compose v2 (bundled with Docker Desktop / modern Docker Engine)
- A Linux or macOS host (Windows with WSL2 is also supported)

---

## Installation

### 1. Clone this repository

```bash
git clone https://github.com/chrisjlapp/ofs-release.git
cd ofs-release
```

### 2. Run the installer

```bash
chmod +x install.sh
./install.sh
```

The installer will:
- Generate all required secrets automatically
- Pull the latest Open Fabric Studio images
- Start all services

### 3. Open the application

Navigate to **http://\<your-server-ip\>** in a browser.

Default credentials are created on first run — log in with the admin account and change your password immediately.

---

## Upgrading

When a new release is available, pull the updated images and restart:

```bash
docker compose pull
docker compose up -d
```

---

## Configuration

All configuration lives in `.env` (auto-created by `install.sh`).

| Variable | Description |
|---|---|
| `APP_PORT` | Host port for the web UI (default: `80`) |
| `CORS_ORIGINS` | Allowed origins — set to your server's hostname or IP |

> **Important:** Never change `CREDENTIAL_ENCRYPTION_KEY` after installation. This key encrypts all stored device credentials — changing it will make them unrecoverable.

---

## Managing Services

```bash
docker compose ps            # check service status
docker compose logs -f       # view live logs (all services)
docker compose logs backend  # logs for a specific service
docker compose down          # stop all services (data is preserved)
docker compose down -v       # stop and DELETE all data (irreversible)
```

---

## Backup

Persistent data (database) is stored in a Docker volume named `postgres_data`.

To back up:
```bash
docker exec ofs-release-db-1 pg_dump -U cfs cfs > ofs_backup_$(date +%Y%m%d).sql
```

To restore:
```bash
docker exec -i ofs-release-db-1 psql -U cfs cfs < ofs_backup_YYYYMMDD.sql
```

Also back up your `.env` file — it contains the encryption key needed to access device credentials.

---


## C9K inital setup

### 1. run through inital setup of switch to generate basic config
then enable netconf with below

```
## make your password from  above priv-15
conf t
aaa new-model
username admin privilege 15 password 0 [password_here]
service password-encryption
## then enable netconf
conf t
netconf-yang
## the first time you enable netconf, it takes about 5-10 min to sync the datastore, so grab a coffee

```


## Support

Still figuring that part out




## License

Licensed under the Apache License 2.0. See [LICENSE](./LICENSE).

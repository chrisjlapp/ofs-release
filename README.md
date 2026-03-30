<div align="center">

<img width="1536" height="1024" alt="OFS_logo_2" src="https://github.com/user-attachments/assets/643da2e6-295c-4ab7-985d-43697858fc77" />

  <defs>
    <linearGradient id="g" x1="15" y1="10" x2="188" y2="188" gradientUnits="userSpaceOnUse">
      <stop offset="0%"   stop-color="#00D8B0"/>
      <stop offset="48%"  stop-color="#2050F5"/>
      <stop offset="100%" stop-color="#9820F8"/>
    </linearGradient>
    <linearGradient id="refl" x1="0" y1="0" x2="0" y2="1">
      <stop offset="0%" stop-color="#2050F5" stop-opacity="0.3"/>
      <stop offset="100%" stop-color="#2050F5" stop-opacity="0"/>
    </linearGradient>
  </defs>
  <g stroke="url(#g)" stroke-linecap="round" fill="none">
    <line x1="100" y1="18"  x2="171" y2="59"  stroke-width="2.5"/>
    <line x1="171" y1="59"  x2="171" y2="141" stroke-width="2.5"/>
    <line x1="171" y1="141" x2="100" y2="182" stroke-width="2.5"/>
    <line x1="100" y1="182" x2="29"  y2="141" stroke-width="2.5"/>
    <line x1="29"  y1="141" x2="29"  y2="59"  stroke-width="2.5"/>
    <line x1="29"  y1="59"  x2="100" y2="18"  stroke-width="2.5"/>
    <line x1="100" y1="18"  x2="100" y2="182" stroke-width="2"/>
    <line x1="171" y1="59"  x2="29"  y2="141" stroke-width="2"/>
    <line x1="171" y1="141" x2="29"  y2="59"  stroke-width="2"/>
    <line x1="100" y1="18"  x2="171" y2="141" stroke-width="2"/>
    <line x1="171" y1="59"  x2="100" y2="182" stroke-width="2"/>
    <line x1="171" y1="141" x2="29"  y2="141" stroke-width="1.8"/>
    <line x1="100" y1="182" x2="29"  y2="59"  stroke-width="2"/>
    <line x1="29"  y1="141" x2="100" y2="18"  stroke-width="2"/>
    <line x1="29"  y1="59"  x2="171" y2="59"  stroke-width="1.8"/>
    <path d="M 35,100 C 50,75 65,75 78,100 C 91,125 105,125 118,100 C 131,75 145,75 162,100" stroke-width="2.8"/>
  </g>
  <g fill="url(#g)">
    <circle cx="100" cy="18"  r="4"/>
    <circle cx="171" cy="59"  r="4"/>
    <circle cx="171" cy="141" r="4"/>
    <circle cx="100" cy="182" r="4"/>
    <circle cx="29"  cy="141" r="4"/>
    <circle cx="29"  cy="59"  r="4"/>
    <circle cx="147" cy="100" r="3.2"/>
    <circle cx="124" cy="59"  r="3.2"/>
    <circle cx="76"  cy="59"  r="3.2"/>
    <circle cx="53"  cy="100" r="3.2"/>
    <circle cx="76"  cy="141" r="3.2"/>
    <circle cx="124" cy="141" r="3.2"/>
    <circle cx="100" cy="100" r="3.5"/>
  </g>
  <ellipse cx="100" cy="196" rx="65" ry="9" fill="url(#refl)"/>
</svg>

<h1>
  <span style="background: linear-gradient(90deg, #00D8B0 0%, #2050F5 48%, #9820F8 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text;">
    Open Fabric Studio
  </span>
</h1>

<h3 style="color: #2050F5; font-weight: 400; margin-top: -8px;">Deployment Guide — v1.1.2</h3>

<p style="color: #6b7280; max-width: 520px;">
  Real-time visual network configuration and monitoring  for professional AV networks. 
  Current target is greenfield installations, unknown results with brownfield.

  Check the current release notes here https://github.com/chrisjlapp/ofs-release/blob/main/RELEASE_NOTES.md

  Check out the User Guide here: https://github.com/chrisjlapp/ofs-release/blob/main/docs/User_Guide/User_Guide.md
  
</p>

</div>

---

## Currently supported Network Hardware

- Catalyst 9000 (validated against 17.15.5)
- Nexus 9300 (validated against 10.5.x)

---

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/) (version 24 or later)
- Docker Compose v2 (bundled with Docker Desktop / modern Docker Engine)
- git installed on host
- A Linux or macOS host (Windows with WSL2 is also supported)
- Validation is done  with Ubuntu 24.04.xx LTS. Report issues with any OS in issues.

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

## if you get a permission error, it is likely your docker permissions, so try the below in the ofs-release directory
sudo docker compose up --build
```

The installer will:
- Generate all required secrets automatically
- Pull the latest Open Fabric Studio images
- Start all services

### 3. Open the application

Navigate to **http://\<your-server-ip\>** in a browser.

Default credentials are created on first run — (admin@ofs.local/admin) log in with the admin account and change your password immediately.

---

## Upgrading

When a new release is available, pull the updated images and restart:

```bash
git pull origin
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

## C9K Initial Setup

### 1. Run through initial setup of switch to generate basic config
Make sure your switch is reachable either through mgmt0 or inband through a SVI for Vlan1 (or other)
Then enable NETCONF with the commands below:

```
## Make your password from above priv-15
conf t
aaa new-model
username admin privilege 15 password 0 [password_here]
service password-encryption
## you may also require
aaa authentication login default local
aaa authorization exec default local

## Then enable NETCONF
conf t
netconf-yang
## The first time you enable NETCONF, it takes about 5-10 min to sync the datastore, so grab a coffee
```

---

## Support

Still figuring that part out.

---

## License

Licensed under the Apache License 2.0. See [LICENSE](./LICENSE).

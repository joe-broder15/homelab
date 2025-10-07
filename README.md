# Homelab Compose Stack

Docker Compose definitions for my homelab services.

## Quick Start
- Create a `.env` file at the repo root and set the environment variables from the table below (use the example as a guide).
- Bring everything up: `docker compose up -d`

## Environment Variables
These variables are read by the Compose files and images. Define them in a `.env` file at the repo root.

| Variable | Used By | Purpose |
| --- | --- | --- |
| `CIFS_HOST` | Crafty, Resilio Sync, Plex | SMB server hostname/IP for mounted volumes |
| `CIFS_SHARE` | Crafty, Resilio Sync | SMB share name for general mounts |
| `CIFS_USER` | Crafty, Resilio Sync, Plex | SMB username for mounts |
| `CIFS_PASS` | Crafty, Resilio Sync, Plex | SMB password for mounts |
| `PLEX_CIFS_SHARE` | Plex | SMB share name for Plex media |
| `TZ` | Crafty, Resilio Sync, Pi-hole | Container timezone (e.g., `Etc/UTC`, `America/Los_Angeles`) |
| `PUID` | Resilio Sync | User ID for file ownership inside container |
| `PGID` | Resilio Sync | Group ID for file ownership inside container |
| `NC_HOST` | DDNS Updater | Namecheap record host (e.g., `server` for `server.example.com`) |
| `NC_DOMAIN` | DDNS Updater | Namecheap root domain (e.g., `example.com`) |
| `NC_PASS` | DDNS Updater | Namecheap Dynamic DNS password |
| `NPM_DB_USER` | Nginx Proxy Manager (app, db) | Database username for NPM |
| `NPM_DB_PASSWORD` | Nginx Proxy Manager (app, db) | Database password for NPM |
| `NPM_DB_NAME` | Nginx Proxy Manager (app, db) | Database name for NPM |
| `NPM_DB_ROOT_PASSWORD` | Nginx Proxy Manager (db) | MariaDB root password |

### .env Example
```
# SMB mounts
CIFS_HOST=192.168.1.10
CIFS_SHARE=homelab
CIFS_USER=your-smb-user
CIFS_PASS=your-smb-pass
PLEX_CIFS_SHARE=media

# Timezone and IDs
TZ=Etc/UTC
PUID=1000
PGID=1000

# Namecheap DDNS
NC_HOST=server
NC_DOMAIN=example.com
NC_PASS=your-ddns-pass

# Nginx Proxy Manager DB
NPM_DB_USER=npm
NPM_DB_PASSWORD=strong-password
NPM_DB_NAME=npm
NPM_DB_ROOT_PASSWORD=strong-root-password
```

## Services
- `services/pihole.yml`: Pi-hole DNS sinkhole.
- `services/resilio-sync.yml`: Resilio Sync for file synchronization.
- `services/crafty.yml`: Crafty Controller for Minecraft servers.
- `services/ddns-updater.yml`: Namecheap DDNS updater.
- `services/plex.yml`: Plex Media Server.
- `services/nginx.yml`: Nginx Proxy Manager with MariaDB backend.

## Compose Entry
The root `docker-compose.yml` includes all service files in `services/` for a single `docker compose up -d`.

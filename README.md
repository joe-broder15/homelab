# Homelab Compose Stack

Docker Compose definitions for my homelab services.

## Quick Start
- Create a `.env` file at the repo root and set the environment variables from the table below (use the example as a guide).
- Bring everything up: `docker compose up -d`

## Hosted Services
- Pi-hole (`services/pihole.yml`) — DNS sinkhole and network-wide ad blocker.
- Resilio Sync (`services/resilio-sync.yml`) — self-hosted file synchronisation.
- Crafty Controller (`services/crafty.yml`) — management UI for Minecraft servers.
- Namecheap DDNS Updater (`services/ddns-updater.yml`) — keeps DNS records pointed at your WAN IP.
- Plex Media Server (`services/plex.yml`) — streaming for the media library mounted over SMB.
- qBittorrent-nox (`services/qbittorrent.yml`) — headless torrent client writing into the Plex media share.

## Environment Variables
These variables are read by the Compose files and images. Define them in a `.env` file at the repo root.

| Variable | Used By | Purpose |
| --- | --- | --- |
| `CIFS_HOST` | Crafty, Resilio Sync, Plex, qBittorrent | SMB server hostname/IP for mounted volumes |
| `CIFS_SHARE` | Crafty, Resilio Sync | SMB share name for Crafty and Resilio SMB mounts |
| `CIFS_USER` | Crafty, Resilio Sync, Plex, qBittorrent | SMB username for mounts |
| `CIFS_PASS` | Crafty, Resilio Sync, Plex, qBittorrent | SMB password for mounts |
| `PLEX_CIFS_SHARE` | Plex, qBittorrent | SMB share name that contains the Plex media library and torrent downloads |
| `NC_HOST` | DDNS Updater | Namecheap record host (e.g., `server` for `server.example.com`) |
| `NC_DOMAIN` | DDNS Updater | Namecheap root domain (e.g., `example.com`) |
| `NC_PASS` | DDNS Updater | Namecheap Dynamic DNS password |

> The compose files currently pin timezone and UID/GID values directly inside the service definitions. Edit the relevant `services/*.yml` file if you need different values for those settings.

### .env Example
```
# SMB mounts
CIFS_HOST=192.168.1.10
CIFS_SHARE=homelab
CIFS_USER=your-smb-user
CIFS_PASS=your-smb-pass
PLEX_CIFS_SHARE=media

# Namecheap DDNS
NC_HOST=server
NC_DOMAIN=example.com
NC_PASS=your-ddns-pass
```

## Services
- `services/pihole.yml`: Pi-hole DNS sinkhole storing configuration inside the `pihole` Docker volume and exposing DNS on `192.168.1.98`.
- `services/resilio-sync.yml`: Resilio Sync with SMB-backed storage at `//${CIFS_HOST}/${CIFS_SHARE}/resilio-sync-content` for synchronized data.
- `services/crafty.yml`: Crafty Controller mounting SMB volumes for backups and imports, plus local directories for logs, configs, and servers.
- `services/ddns-updater.yml`: Namecheap Dynamic DNS updater using the credentials from `.env`.
- `services/plex.yml`: Plex Media Server running in host network mode with SMB media storage and dedicated config/transcode volumes.
- `services/qbittorrent.yml`: qBittorrent-nox writing downloads into the same SMB share Plex reads from.

## Service Ports

- Pi-hole (`services/pihole.yml`)
  - `192.168.1.98:53->53/tcp` — DNS
  - `192.168.1.98:53->53/udp` — DNS
  - `8443->443/tcp` — Admin UI (HTTPS)
- Resilio Sync (`services/resilio-sync.yml`)
  - `8888->8888/tcp` — Web UI
  - `55555->55555/udp` — Sync data
- Crafty (`services/crafty.yml`)
  - `7443->8443/tcp` — HTTPS UI
  - `8123->8123/tcp` — Dynmap
  - `19132->19132/udp` — Bedrock
  - `25500-25600->25500-25600/tcp` — MC server range
- DDNS Updater (`services/ddns-updater.yml`)
  - No ports exposed
- Plex (`services/plex.yml`)
  - Host network — commonly `32400/tcp`, `1900/udp`, `32469/tcp`, `5353/udp`, `32410-32414/udp`
- qBittorrent-nox (`services/qbittorrent.yml`)
  - `6881->6881/tcp` — BitTorrent peer traffic
  - `6881->6881/udp` — DHT/peer discovery
  - `8080->8080/tcp` — Web UI

## Compose Entry
The root `docker-compose.yml` is a thin include file. List whichever `services/*.yml` definitions you want to run there, then launch the stack with `docker compose up -d` from the repo root.

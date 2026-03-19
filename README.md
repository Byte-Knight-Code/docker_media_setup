# Docker Media Server Setup

A complete Docker stack for a self-hosted media server with automated content management.

## Overview

This stack provides:

| Service | Purpose | Default Port |
|---------|---------|--------------|
| **Jellyfin** | Media streaming server (movies, TV shows, music, books) | 8096 |
| **Sonarr** | Automated TV show management and downloads | 8989 |
| **Radarr** | Automated movie management and downloads | 7878 |
| **Prowlarr** | Indexer manager and torrent proxy | 9696 |
| **qBittorrent** | Torrent download client | 8080 |
| **Jellyseerr** | Media request system for multiple users | 5055 |
| **Homepage** | Unified dashboard for all services | 3000 |

---

## Documentation Links

### Official Documentation

| Service | Docker Hub | GitHub |
|---------|------------|--------|
| **Jellyfin** | [jellyfin/jellyfin](https://hub.docker.com/r/jellyfin/jellyfin) | [jellyfin/jellyfin](https://github.com/jellyfin/jellyfin) |
| **Sonarr** | [lscr.io/linuxserver/sonarr](https://hub.docker.com/r/linuxserver/sonarr) | [Sonarr/Sonarr](https://github.com/Sonarr/Sonarr) |
| **Radarr** | [lscr.io/linuxserver/radarr](https://hub.docker.com/r/linuxserver/radarr) | [Radarr/Radarr](https://github.com/Radarr/Radarr) |
| **Prowlarr** | [lscr.io/linuxserver/prowlarr](https://hub.docker.com/r/linuxserver/prowlarr) | [Prowlarr/Prowlarr](https://github.com/Prowlarr/Prowlarr) |
| **qBittorrent** | [lscr.io/linuxserver/qbittorrent](https://hub.docker.com/r/linuxserver/qbittorrent) | [qbittorrent/qBittorrent](https://github.com/qbittorrent/qBittorrent) |
| **Jellyseerr** | [fallenbagel/jellyseerr](https://hub.docker.com/r/fallenbagel/jellyseerr) | [fallenbagel/jellyseerr](https://github.com/fallenbagel/jellyseerr) |
| **Homepage** | [ghcr.io/gethomepage/homepage](https://hub.docker.com/r/ghcr.io/gethomepage/homepage) | [gethomepage/homepage](https://github.com/gethomepage/homepage) |

### Community Resources

| Service | Wiki |
|---------|------|
| **Jellyfin** | [jellyfin.org/wiki](https://jellyfin.org/docs/) |
| **Sonarr/Radarr** |[wiki.servarr.com](https://wiki.servarr.com/) [wiki.servarr.com](https://wiki.servarr.com/en/sonarr)|
| **Prowlarr** |  [wiki.servarr.com](https://wiki.servarr.com/prowlarr) |
| **qBittorrent** | [github.com/qbittorrent](https://github.com/qbittorrent/qBittorrent/wiki) |
| **Jellyseerr** | [docs.seerr.dev](https://docs.seerr.dev/) |
| **Homepage** | [gethomepage.dev](https://gethomepage.dev/configs/) |

---

## Prerequisites

- Ubuntu Server 22.04 LTS or 24.04 LTS
- SSH access to your server
- Internet connection
- Basic understanding of Docker and command line

---

## Initial Server Setup

Update your system packages:

```bash
sudo apt update && sudo apt upgrade
```

---

## Install Docker Engine

### 1. Install required dependencies

```bash
sudo apt install ca-certificates curl gnupg lsb-release
```

### 2. Add Docker's official GPG key

```bash
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

For more details, see the [official Docker installation guide](https://docs.docker.com/engine/install/ubuntu/).

### 3. Add the repository to Apt sources

```bash
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Signed-By: /etc/apt/keyrings/docker.asc
EOF
sudo apt update
```

### 4. Install Docker Engine

```bash
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### 5. Verify installation

```bash
sudo systemctl status docker
sudo docker run hello-world
```

If Docker doesn't start automatically:

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

---

## Add Your User to the Docker Group

This allows running Docker commands without `sudo`:

```bash
sudo usermod -aG docker $USER
```

Log out and back in, or run:

```bash
newgrp docker
```

---

## Clone This Repository

```bash
git clone https://github.com/Byte-Knight-Code/docker_media_setup.git
cd docker_media_setup
```

---

## Create Required Directories

```bash
mkdir -p config media/downloads media/jelly_media/movies media/jelly_media/shows
```

These directories are mounted into the containers for persistent storage.

---

## Directory Structure

```
docker_media_setup/
├── config/              # Service configuration files (persistent) - NOT tracked in git
│   ├── jellyfin/        # Jellyfin config
│   ├── sonarr/          # Sonarr config
│   ├── radarr/          # Radarr config
│   ├── prowlarr/        # Prowlarr config
│   ├── qbittorrent/     # qBittorrent config
│   ├── jellyseerr/      # Jellyseerr config
│   └── homepage/        # Homepage config
├── media/               # Media files (NOT tracked in git)
│   ├── downloads/       # qBittorrent download location
│   └── jelly_media/
│       ├── movies/      # Radarr imports movies here
│       └── shows/       # Sonarr imports TV shows here
├── docs/                # Documentation
├── docker-compose.yml    # Container orchestration
└── README.md             # This file
```

---

## Start the Stack

### Start all services

```bash
docker compose up -d
```

### Check container status

```bash
docker compose ps
```

### View logs

```bash
# All services
docker compose logs

# Specific service
docker compose logs jellyfin

# Follow logs in real-time
docker compose logs -f
```

---

## Access the Services

| Service | URL | Default Credentials |
|---------|-----|-------------------|
| Jellyfin | http://your-server:8096 | Set during first-run wizard |
| Sonarr | http://your-server:8989 | Set during initial setup |
| Radarr | http://your-server:7878 | Set during initial setup |
| Prowlarr | http://your-server:9696 | Set during initial setup |
| qBittorrent | http://your-server:8080 | admin / adminadmin |
| Jellyseerr | http://your-server:5055 | First user becomes admin |
| Homepage | http://your-server:3000 | No authentication by default |

> **Important:** Change all default passwords immediately after setup!

---

## Initial Configuration

### Finding Your Server IP

```bash
ip a
```

Look for your network interface (usually `eth0` or `en0`) and note the IP address (e.g., `192.168.1.100`).

---

### Homepage (http://your-server:3000)
1. Access the dashboard
2. Edit `config/homepage/services.yaml` to update localhost to your server's IP
3. Services will auto-detect running containers
For advanced configuration, see the [Homepage documentation](https://gethomepage.dev/).

---

### Jellyfin (http://your-server:8096)
1. Complete the first-run wizard
2. Add your media library pointing to `/jelly_media`
3. Create libraries for:
   - Movies (`/jelly_media/movies`)
   - TV Shows (`/jelly_media/shows`)
4. Configure metadata agents (TMDb, TVDB)
For more details, see the [Jellyfin documentation](https://jellyfin.org/docs/).

---

### qBittorrent (http://your-server:8080)
1. Log in with default credentials (admin / adminadmin)
2. Go to **Tools > Options**
3. Change the admin password under **Web UI**
4. Configure download settings:
   - Set download directory to `/downloads`
   - Configure seed ratio limits
5. Consider enabling VPN split tunneling
For more details, see the [qBittorrent documentation](https://www.qbittorrent.org/).

---

### Prowlarr (http://your-server:9696)
Prowlarr manages your indexers (torrent trackers) across all services.
1. Complete initial setup and create admin account
2. Go to **Settings > Indexers**
3. Add your preferred indexers:
   - Public indexers: 1337x, RARBG, YTS, etc.
   - Private trackers: Add your invites here
4. Configure seeders/leecher requirements per indexer
5. Go to **Settings > Apps**
6. Add Sonarr and Radarr as applications:
   - Sonarr: http://sonarr:8989
   - Radarr: http://radarr:7878
   - Use the API key from each service
For more details, see the [Prowlarr documentation](https://wiki.servarr.com/prowlarr).

---

### Sonarr (http://your-server:8989)
Sonarr automatically downloads and manages TV shows.
1. Complete initial setup
2. Go to **Settings > Media Management**
   - Enable "Rename Episodes"
   - Configure your preferred naming scheme
3. Go to **Settings > Indexers**
   - Click "Add Indexer"
   - Select "Prowlarr" and import your indexers
4. Go to **Settings > Download Clients**
   - Add qBittorrent:
     - Host: `qbittorrent`
     - Port: `8080`
     - Username/Password: Your qBittorrent credentials
5. Add your TV library:
   - Root folder: `/shows`
   - Monitor: "All Episodes"
For more details, see the [Sonarr documentation](https://wiki.servarr.com/sonarr).

---

### Radarr (http://your-server:7878)
Radarr automatically downloads and manages movies.
1. Complete initial setup
2. Go to **Settings > Media Management**
   - Enable "Rename Movies"
   - Configure your preferred naming scheme
3. Go to **Settings > Indexers**
   - Add via Prowlarr (same as Sonarr)
4. Go to **Settings > Download Clients**
   - Add qBittorrent (same as Sonarr)
5. Add your movie library:
   - Root folder: `/movies`
For more details, see the [Radarr documentation](https://wiki.servarr.com/radarr).

---

### Jellyseerr (http://your-server:5055)
Jellyseerr allows users to request media through a web interface.
1. Complete initial setup (first user becomes admin)
2. Go to **Settings > Services**
3. Connect Jellyfin:
   - Hostname: `http://jellyfin:8096`
   - API Key: Found in Jellyfin > Dashboard > API Key
4. Connect Sonarr:
   - Hostname: `http://sonarr:8989`
   - API Key: Found in Sonarr > Settings > General
5. Connect Radarr:
   - Hostname: `http://radarr:7878`
   - API Key: Found in Radarr > Settings > General
6. Configure notification settings as desired
For more details, see the [Jellyseerr documentation](https://github.com/fallenbagel/jellyseerr).

---

## Media Flow

```
┌─────────────┐     ┌──────────────┐     ┌────────────┐
│   Prowlarr  │────>│   Sonarr /   │────>│   qBittor  │
│  (Indexers) │     │   Radarr     │     │   rent     │
└─────────────┘     └──────────────┘     └─────┬──────┘
                            │                    │
                            v                    v
                     ┌──────────────┐     ┌────────────┐
                     │  Hard Links  │<────│ Downloads  │
                     │  (completed)│     │            │
                     └──────┬───────┘     └────────────┘
                            │
                            v
                     ┌──────────────┐     ┌────────────┐
                     │   Jellyfin   │<────│   Media    │
                     │   (Streams) │     │   Files    │
                     └──────────────┘     └────────────┘

```

- **Prowlarr** searches torrent trackers for content
- **Sonarr/Radarr** coordinate downloads via **qBittorrent**
- Completed downloads are hard-linked to media folders (saves disk space)
- **Jellyfin** serves media to your devices

---

## Useful Commands

### Container Management

```bash
# Start the stack
docker compose up -d
# Stop the stack
docker compose down
# Restart a specific service
docker compose restart jellyfin
# View logs
docker compose logs -f jellyfin
# Update all images
docker compose pull
# Update and restart
docker compose pull && docker compose up -d
```

### Inspecting Containers

```bash
# Enter a container shell
docker exec -it jellyfin /bin/bash
# Check resource usage
docker stats
# View container configuration
docker inspect jellyfin
```

---

## Security Recommendations

### 1. Change Default Passwords

Change these immediately after setup:
- qBittorrent admin password
- Jellyfin admin password
- Sonarr/Radarr/Prowlarr API keys and passwords

### 2. Use a VPN

qBittorrent traffic should go through a VPN with:
- Kill switch enabled
- Split tunneling (only qBittorrent uses VPN)

Recommended: [Mullvad VPN](https://mullvad.net/) - no-logs policy, supports split tunneling.

### 3. Firewall Configuration

```bash
# Allow SSH
sudo ufw allow 22/tcp
# Allow access only from your network
sudo ufw allow from 192.168.1.0/24 to any port 8096,8989,7878,9696,8080,5055,3000
# Enable firewall
sudo ufw enable
```

### 4. Reverse Proxy with SSL

For external access, use a reverse proxy with Let's Encrypt:
- [Nginx Proxy Manager](https://nginxproxymanager.com/)
- [Traefik](https://traefik.io/)
- [Caddy](https://caddyserver.com/)

### 5. Regular Updates

```bash
# Update Docker images weekly
docker compose pull && docker compose up -d
```

---

## Troubleshooting

### Containers won't start

```bash
# Check logs
docker compose logs
# Verify docker-compose.yml syntax
docker compose config
```

### Permission issues

```bash
# Set correct ownership
sudo chown -R 1000:1000 ./config ./media
```

### Port conflicts

If a port is already in use, edit `docker-compose.yml` to change the port mapping.

### Health checks failing

```bash
# Restart with fresh containers
docker compose down
docker compose up -d
```

---

## Service-Specific Troubleshooting

### Jellyfin
- **Library not scanning?** Check folder permissions and metadata settings
- **Transcoding issues?** Ensure hardware acceleration is configured (VAAPI, NVENC)

### Sonarr/Radarr
- **Download not starting?** Check qBittorrent connection and category settings
- **Hard links failing?** Ensure source and destination are on the same filesystem
- **Import errors?** Verify folder permissions (1000:1000 UID/GID)

### qBittorrent
- **Web UI not loading?** Check logs: `docker compose logs qbittorrent`
- **Slow downloads?** Check seed ratio settings and VPN connection

### Homepage
- **Services not detected?** Ensure container names match in `services.yaml`
- **Wrong links?** Update IP addresses in `services.yaml`

---

## Extending the Stack

### Adding Services

Edit `docker-compose.yml` to add more services:

```yaml
services:
  # ... existing services ...
  new-service:
    image: some/image
    container_name: new-service
    volumes:
      - ./config/new-service:/config
    ports:
      - "8081:8081"
```

### Environment Variables

Create a `.env` file for sensitive configuration:

```bash
# .env
PUID=1000
PGID=1000
TZ=America/New_York
```

---

## Additional Resources

### Servarr Wiki

The [Servarr Wiki](https://wiki.servarr.com/) is the authoritative source for:
- Sonarr/Radarr/Prowlarr configuration
- Indexer setup guides
- Download client configuration
- Troubleshooting tips

### Docker Compose Documentation
- [Docker Compose CLI](https://docs.docker.com/compose/reference/)
- [Compose file reference](https://docs.docker.com/compose/compose-file/)

### Community Guides
- [TRaSH Guides](https://trash-guides.info/) - Hard linking and quality settings
- [Hotio's Docker Guide](https://hotio.dev/) - Alternative images and setup guides

---

## Contributing

Found an issue or have improvements? Open an issue or pull request on GitHub.

---

## License

This configuration is provided as-is for personal use..

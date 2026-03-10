# Homelab Infrastructure

Ansible-managed homelab infrastructure running on Proxmox VE with security-focused VM segmentation and network isolation.

## Architecture

### Network Diagrams

- [Physical Network Topology](docs/architecture/physical-layer.png) - Hardware layout and physical connections
- [Logical Network Topology](docs/architecture/logical-network.png) - Network segmentation and service architecture

### Infrastructure Overview

**Hardware:**
- Dell OptiPlex 7020 Micro
  - Intel Core i5-12500T
  - 64GB RAM
  - 512GB SSD
  - Proxmox VE 8.4
- 5TB ZFS pool "tank" with NFS shares
- Raspberry Pi 4B 8GB (Pi-hole DNS)

**Network Segmentation:**
- Main network: `192.168.1.0/24` (home devices)
- Homelab subnet: `192.168.100.0/24` (isolated VM network)
- Router VM bridges between networks

### Virtual Machines

| VM | IP | Services | Purpose |
|---|---|---|---|
| router | 192.168.1.159 / 192.168.100.1 | gateway, NAT | Network bridge between subnets |
| proxy | 192.168.100.10 | Caddy | Reverse proxy, ingress/edge |
| streaming | 192.168.100.11 | Jellyfin, Jellyseerr | Media streaming, public-facing |
| arr | 192.168.100.12 | Radarr, Sonarr, Prowlarr, Bazarr | Media automation |
| downloader | 192.168.100.13 | qBittorrent, gluetun | Downloads via ProtonVPN with kill switch |
| monitoring | 192.168.100.14 | Homepage, Uptime Kuma, Speedtest-tracker | Observability |

### Storage

- ZFS pool "tank" (5TB) provides NFS shares
- Mounted by: streaming VM, arr VM, downloader VM

## Security Features

- **Network isolation:** Homelab subnet separated from main network
- **VPN kill switch:** Downloader VM routes all traffic through ProtonVPN via gluetun
- **Service segmentation:** Each service type runs on dedicated VM
- **Reverse proxy:** External access only through Caddy
- **DNS filtering:** Pi-hole for network-wide ad blocking

## Internal DNS (.lan domains)

All services accessible via Pi-hole local DNS:
- `jellyfin.lan`, `jellyseerr.lan`, `radarr.lan`, `sonarr.lan`
- `prowlarr.lan`, `bazarr.lan`, `qbittorrent.lan`
- `homepage.lan`, `uptime.lan`, `speedtest.lan`

## Project Structure
```
.
├── ansible.cfg
├── inventory/
│   ├── hosts.yml
│   └── group_vars/
│       └── all.yml
├── playbooks/
├── roles/
└── docs/
    └── architecture/
        ├── physical-layer.drawio
        ├── physical-layer.png
        ├── logical-network.drawio
        └── logical-network.png
```

## Notes

- Pi-hole DNS server runs on Raspberry Pi (192.168.1.225), managed separately
- Infrastructure-as-code approach using Ansible for reproducibility
- All secrets managed via Ansible Vault
- DHCP Server on Router
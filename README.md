# Homelab Infrastructure

Ansible-managed homelab infrastructure with security-focused VM segmentation.

## Architecture

| VM | Purpose | Services |
|---|---|---|
| proxy | ingress/edge | caddy |
| streaming | public-facing apps | jellyfin, jellyseerr |
| arr | media automation | radarr, sonarr, prowlarr, bazarr |
| downloader | untrusted traffic (VPN) | qbittorrent |
| paperless | document management | paperless-ngx, postgres, redis |
| monitoring | observability | uptime kuma, homepage, speedtest-tracker |
| rss | read-later/feeds | wallabag, freshrss, postgres |

## Network Design

- Static IPs: `192.168.1.10-16` (VMs)
- DHCP range: `192.168.1.20-254` (clients)
- Public access: Jellyfin/Jellyseerr via Cloudflare tunnel
- Admin access: VPN only
- Downloader VM: Routed through ProtonVPN with kill switch

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
```

## Roadmap

- [x] Inventory setup
- [ ] Base VM template
- [ ] Base OS hardening playbook
- [ ] Network segmentation / firewall rules
- [ ] Service deployment per VM
- [ ] Caddy reverse proxy config
- [ ] Cloudflare tunnel setup
- [ ] Monitoring & backups

## Notes

- Pi-hole runs separately on Raspberry Pi (192.168.1.225), not managed by Ansible
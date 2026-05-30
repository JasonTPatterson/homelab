# Homelab

A self-hosted infrastructure lab running on a Raspberry Pi 4, providing network-wide ad blocking, remote access, game hosting, and full-stack service monitoring — all self-hosted and self-managed.

---

## Hardware

| Component | Spec |
|---|---|
| SBC | Raspberry Pi 4 Model B — 4GB RAM |
| Network | Wired Gigabit Ethernet |
| ISP | Brightspeed — 2 Gbps symmetrical fiber |
| Router | Calix GigaSpire BLAST |
| Connection type | Static IP + DHCP reservation |

---

## Services

| Service | Purpose | Access |
|---|---|---|
| [Pi-hole v6](https://pi-hole.net/) | Network-wide DNS ad blocking | https://pihole.home |
| [Tailscale](https://tailscale.com/) | Mesh VPN + exit node | — (WireGuard UDP) |
| [Minecraft BDS](https://www.minecraft.net/en-us/download/server/bedrock) | Bedrock Dedicated Server (box64) | 19132 UDP |
| [Portainer](https://www.portainer.io/) | Docker container management | https://portainer.home |
| [Uptime Kuma](https://github.com/louislam/uptime-kuma) | Service health monitoring | https://uptime-kuma.home |
| [Homepage](https://gethomepage.dev/) | Live dashboard | https://homepage.home |
| [Homarr](https://homarr.dev/) | Dashboard (secondary) | https://homarr.home |
| [Traefik](https://traefik.io/) | Reverse proxy + HTTPS | https://traefik.home |

---

## Network Configuration

```
ISP (2 Gbps fiber)
  └── Calix GigaSpire BLAST
        ├── DHCP reservation → Pi 192.168.1.139 (static)
        ├── DNS override → 192.168.1.139 (pushed to all devices via DHCP)
        ├── Port forward 19132 UDP → Pi (Minecraft Bedrock)
        └── Gigabit LAN → Raspberry Pi 4
```

**DNS:** Router DHCP pushes `192.168.1.139` as the upstream DNS resolver for every device on the network. All queries pass through Pi-hole before forwarding to upstream resolvers. Custom `.home` local DNS records served via Pi-hole for internal service discovery.

**VPN:** Tailscale exit node enables full tunnel remote access — all traffic routes through the Pi from any device, anywhere.

**Reverse Proxy:** Traefik routes all internal services via `.home` domains with self-signed HTTPS. No more IP:port access — every service has a clean URL.

---

## Pi-hole Stats

| Metric | Value |
|---|---|
| Blocklist | 551,426 domains |
| Block rate | ~46% |
| Coverage | Every device on the network |
| Version | v6.4.2 (FTL v6.6.2) |

---

## Minecraft Server

Official Bedrock Dedicated Server running on ARM64 via **box64** x86_64 emulation. Port forwarded at the router level and publicly accessible. No wrapper, no third-party server software — stock BDS binary translated at runtime.

---

## Stack

```
OS             Debian 12 (Raspberry Pi OS)
Runtime        Docker + Docker Compose v2
Emulation      box64 (x86_64 → ARM64)
VPN            Tailscale (WireGuard)
DNS            Pi-hole v6
Reverse Proxy  Traefik (file provider, HTTPS)
Monitoring     Uptime Kuma + Homepage
```

---

## Infrastructure Notes

- All persistent service data is bind-mounted to host directories — no anonymous Docker volumes
- Traefik introduced as reverse proxy to centralize service routing and eliminate IP:port access patterns, moving toward production-style internal service architecture
- Homepage configured with live Pi-hole stats (v6 API), Open-Meteo weather, system resource monitoring
- Uptime Kuma monitors all services with alerting
- Portainer provides a UI for container lifecycle management without needing SSH for routine ops
- Tailscale exit node means the Pi is the single outbound path for remote sessions — full tunnel, not split

---

## Skills Demonstrated

- Linux system administration
- Docker container orchestration
- Reverse proxy configuration (Traefik)
- DNS and network configuration
- VPN deployment and remote networking
- Service monitoring and uptime management
- Infrastructure troubleshooting
- Self-hosted service management
- ARM64/x86_64 compatibility handling
- AI-assisted workflows and troubleshooting

---

## Planned Improvements

- Automated world backups (Minecraft)
- ~~Reverse proxy implementation~~ ✅ Traefik deployed
- ~~SSL/TLS for internal services~~ ✅ Self-signed HTTPS on all services
- Infrastructure as Code deployment
- Centralized logging
- Zero 2W dedicated PiHole/Tailscale node
- BMO — local AI agent on Pi 5

---

*Self-hosted. Self-managed. Self-taught.*
```
<img width="1680" height="1930" alt="image" src="https://github.com/user-attachments/assets/5245ccb6-2e5b-4986-84a0-28b4c707b52c" />

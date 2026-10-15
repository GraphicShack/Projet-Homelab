# Proxmox Homelab

A personal homelab built to practice virtualization, Linux administration, networking, self-hosting, remote access, and cybersecurity fundamentals.

> **Public-repo note:** IP addresses, credentials, API tokens, certificates, and other sensitive values are intentionally omitted or replaced with examples.

## What is running

- **Proxmox VE** — hypervisor and central management
- **Ubuntu Server LXC** — file services and Linux administration
- **Samba** — SMB file sharing
- **Tailscale** — private remote access to the homelab
- **AdGuard Home** — network-level DNS filtering
- **Nginx Proxy Manager** — reverse proxy management
- **Homarr** — homelab dashboard
- **Windows 11 VM** — Windows lab environment

## Architecture

```mermaid
flowchart TD
    Internet --> Router[Home Router]
    Router --> PVE[Proxmox VE]

    PVE --> Ubuntu[Ubuntu Server LXC]
    PVE --> AdGuard[AdGuard Home LXC]
    PVE --> NPM[Nginx Proxy Manager LXC]
    PVE --> Homarr[Homarr LXC]
    PVE --> Windows[Windows 11 VM]

    Ubuntu --> Samba[Samba / SMB]
    Ubuntu --> Tailscale[Tailscale]
    NPM --> AdGuard
    NPM --> Homarr
```

## Skills practiced

- Proxmox VE administration
- LXC containers and virtual machines
- Linux networking and troubleshooting
- SMB/Samba file sharing
- DNS and network filtering
- Docker and Docker Compose
- Reverse proxies
- VPN-style private networking with Tailscale
- Basic service hardening and secret-management practices
- Troubleshooting from logs, routes, interfaces, and service status

## Documentation

- [Architecture](docs/01-architecture.md)
- [Proxmox setup](docs/02-proxmox.md)
- [Ubuntu file server](docs/03-ubuntu-samba.md)
- [Remote access with Tailscale](docs/04-tailscale.md)
- [AdGuard Home](docs/05-adguard-home.md)
- [Nginx Proxy Manager](docs/06-nginx-proxy-manager.md)
- [Homarr](docs/07-homarr.md)
- [Windows 11 VM](docs/08-windows-vm.md)
- [Troubleshooting notes](docs/09-troubleshooting.md)
- [Project journal](JOURNAL.md)

## Security approach

This repository never stores real passwords, private keys, API secrets, Tailscale addresses, or authentication tokens. Configuration examples use placeholders only.

## Screenshots

The screenshots below are sanitized for public sharing. Credentials, API tokens, account identifiers, certificates, and remote-access addresses are not exposed.

### Proxmox VE

![Proxmox VE dashboard](screenshots/proxmox-dashboard.png)

### AdGuard Home

![AdGuard Home dashboard](screenshots/adguard-home-dashboard.png)

### Nginx Proxy Manager

![Nginx Proxy Manager dashboard](screenshots/nginx-proxy-manager-dashboard.png)

## Next steps

- Add Uptime Kuma for service monitoring
- Deploy Immich for photo management
- Add a dedicated cybersecurity monitoring stack
- Improve local DNS names for internal services
- Add backups and recovery documentation

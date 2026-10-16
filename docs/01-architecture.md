# Architecture

The homelab uses Proxmox VE as the virtualization layer. Services are separated into lightweight LXC containers where practical, while full operating-system labs use virtual machines.

## Design goals

- Keep infrastructure modular.
- Isolate services so one failure does not affect the whole server.
- Use private remote access instead of exposing management interfaces directly to the Internet.
- Keep public documentation free of real credentials and sensitive addressing.

## Service layout

| Service | Type | Purpose |
|---|---|---|
| Proxmox VE | Host | Hypervisor and management |
| Ubuntu Server | LXC | File server and general Linux services |
| AdGuard Home | LXC | DNS filtering |
| Nginx Proxy Manager | LXC + Docker | Reverse proxy |
| Homarr | LXC + Docker | Dashboard |
| Windows 11 | VM | Windows lab environment |


## Physical layer

The Proxmox host is a repurposed desktop rather than purpose-built server hardware.

Because a direct Ethernet run was not available at the original server location, the first version of the network relied on a wireless repeater / bridge feeding Ethernet to the host.

This was functional, but it also created an important performance constraint for file transfers and photo access.

```text
Client -> Wi-Fi -> Router -> Wi-Fi -> Repeater -> Ethernet -> Proxmox
```

This is intentionally documented because it explains why some performance issues were network-related rather than CPU, RAM, or storage-related.

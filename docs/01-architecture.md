# Architecture

## 🇫🇷 Français

Le homelab utilise Proxmox VE comme couche de virtualisation. Les services légers sont séparés dans des LXC containers quand ça fait du sens, tandis que les environnements complets utilisent des VMs.

## Objectifs de design

- garder l’infrastructure modulaire;
- isoler les services pour éviter qu’un problème en casse plusieurs;
- utiliser un accès privé à distance au lieu d’exposer les interfaces de management directement sur Internet;
- garder la documentation publique sans credentials ni adressage sensible.

## Layout des services

| Service | Type | Rôle |
|---|---|---|
| Proxmox VE | Host | Hypervisor et management |
| Ubuntu Server | LXC | File server et services Linux |
| AdGuard Home | LXC | DNS filtering |
| Nginx Proxy Manager | LXC + Docker | Reverse proxy |
| Homarr | LXC + Docker | Dashboard |
| Windows 11 | VM | Environnement Windows pour les labs |

## Physical layer

Le host Proxmox est un desktop recyclé, pas un server dédié.

Comme il n’y avait pas de câble Ethernet direct à l’endroit où le serveur était installé, la première version du réseau utilisait un wireless repeater / bridge qui envoyait ensuite Ethernet vers Proxmox.

Ça fonctionnait, mais ça pouvait devenir un bottleneck pour les transferts de fichiers et les photos.

```text
Client -> Wi-Fi -> Router -> Wi-Fi -> Repeater -> Ethernet -> Proxmox
```

Je garde cette partie dans la documentation parce qu’elle explique pourquoi certains problèmes de performance venaient du réseau et non du CPU, de la RAM ou du storage.

---

## 🇬🇧 English

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

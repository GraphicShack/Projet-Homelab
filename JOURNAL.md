# Homelab Journal

## 🇨🇦 Français

Ce fichier suit l’évolution du projet. Le but n’est pas juste de montrer ce qui marche, mais aussi ce qui a cassé, pourquoi, et comment je l’ai réglé.

## Phase 0 — Transformer un vieux desktop en serveur

Avant que Proxmox soit réellement utile, il fallait déjà que le matériel devienne un serveur qui peut rouler en continu.

- recyclage d’un vieux desktop au lieu d’acheter du hardware dédié;
- utilisation d’un NVMe interne pour Proxmox et le storage des VMs/LXC;
- pas de câble Ethernet direct disponible près du serveur;
- réflexion autour d’un Wi-Fi adapter, powerline et d’un repeater/bridge;
- activation de Intel VMX / VT-x dans le BIOS après une erreur KVM;
- ajout d’un external drive pour le futur Samba file server;
- constat assez rapide que le placement physique et le réseau peuvent être le bottleneck même si le serveur lui-même va bien.

### Ce que j’ai retenu
Le hardware et le réseau physique comptent autant que la config software. Avoir plus de CPU ou de RAM ne règle pas un uplink Wi-Fi faible.

## Phase 1 — Base Proxmox

- installation de Proxmox VE;
- activation de hardware virtualization dans le BIOS;
- configuration d’une adresse de management fixe sur le LAN;
- création des premiers LXC containers et VMs.

### Ce que j’ai retenu
Un hypervisor peut être parfaitement en santé mais quand même inaccessible si l’interface réseau ou le bridge est mal configuré. Les commandes `ip -br link`, `ip -br addr` et `ip route` sont devenues mes premiers réflexes.

## Phase 2 — Ubuntu file server

- création d’un Ubuntu Server LXC;
- montage du storage externe;
- configuration de Samba;
- tests à partir de macOS et Android.

### Ce que j’ai retenu
Les permissions Linux et le UID/GID mapping deviennent importants dès qu’on bind-mount du storage dans un unprivileged LXC.

## Phase 3 — Accès à distance

- installation de Tailscale sur Ubuntu Server;
- ajout de `/dev/net/tun` au LXC;
- validation de l’accès à partir d’un autre réseau;
- accès au Samba share via le réseau privé Tailscale.

### Ce que j’ai retenu
Un service peut écouter correctement sur un port mais rester inaccessible à cause du routing, des ACLs ou des restrictions du container.

## Phase 4 — DNS filtering

- déploiement d’AdGuard Home;
- configuration des clients pour utiliser AdGuard comme DNS;
- validation des requêtes directement dans le dashboard.

### Ce que j’ai retenu
Installer un DNS filter ne suffit pas. Si les clients ne l’utilisent pas, le dashboard reste à zéro et rien n’est filtré.

## Phase 5 — Reverse proxy

- création d’un LXC dédié à Nginx Proxy Manager;
- installation de Docker et Docker Compose;
- déploiement de Nginx Proxy Manager;
- début de l’utilisation de hostnames plus propres au lieu de mémoriser chaque IP:port.

### Highlight de troubleshooting
Le LXC avait seulement une IPv6 link-local address et aucune route IPv4 utilisable. Activer IPv4 DHCP sur `net0` a réglé `Network is unreachable` ainsi que les erreurs de package download.

## Phase 6 — Dashboard

- déploiement de Homarr;
- intégration de Proxmox et des autres services;
- troubleshooting des Proxmox API tokens et de la confiance du certificat TLS.

## Phase 7 — Windows lab VM

- création d’une Windows 11 VM;
- chargement des VirtIO storage drivers;
- installation sur un virtual disk VirtIO;
- utilisation comme environnement Windows pour les labs.

## Prévu ensuite

- Uptime Kuma
- Immich
- meilleure stratégie de backup
- monitoring cybersécurité / expériences SIEM
- plus d’automation et de documentation

---

## 🇬🇧 English

# Homelab Journal

This file tracks the project as it evolves. It focuses on what was built, what failed, and what was learned.

## Phase 0 — Turning an old desktop into a server

Before Proxmox was useful, the hardware itself had to become a practical always-on server.

- Repurposed an older desktop tower instead of buying dedicated server hardware.
- Used an internal NVMe drive for the Proxmox installation and VM/container storage.
- Worked around the lack of a direct Ethernet run near the server.
- Considered Wi-Fi adapters, powerline networking, and a repeater/bridge solution.
- Enabled Intel VMX / VT-x in BIOS after Proxmox reported that hardware-accelerated KVM was unavailable.
- Integrated external storage for the future Samba file server.
- Learned quickly that network placement and physical topology can bottleneck an otherwise healthy server.

### Lesson learned
The physical layer matters. CPU, RAM, and software configuration are only part of a homelab; network quality, storage interfaces, BIOS settings, and where the machine physically lives can be just as important.

## Phase 1 — Proxmox foundation

- Installed Proxmox VE on a dedicated desktop.
- Enabled hardware virtualization in BIOS.
- Configured a static management address on the home LAN.
- Created initial LXC containers and VMs.

### Lesson learned
A hypervisor can be healthy while still being unreachable if the physical interface or bridge has no carrier or a bad network configuration. Checking `ip -br link`, `ip -br addr`, and routes became the first troubleshooting step.

## Phase 2 — Ubuntu file server

- Created an Ubuntu Server LXC.
- Mounted external storage into the container.
- Configured Samba for SMB file sharing.
- Tested access from macOS and Android.

### Lesson learned
Filesystem permissions and LXC UID/GID mapping matter when passing host storage into an unprivileged container.

## Phase 3 — Remote access

- Installed Tailscale on the Ubuntu Server LXC.
- Passed `/dev/net/tun` into the LXC.
- Verified remote access from outside the home network.
- Accessed Samba over the private Tailscale network.

### Lesson learned
A service can be listening correctly while still being unreachable remotely because of routing, ACLs, or container device restrictions.

## Phase 4 — DNS filtering

- Deployed AdGuard Home in a dedicated LXC.
- Configured clients to use AdGuard as their DNS resolver.
- Verified DNS queries and filtering from the dashboard.

### Lesson learned
Installing a DNS filter is not enough: clients must actually use it. A dashboard showing zero DNS queries is a strong clue that traffic is bypassing the resolver.

## Phase 5 — Reverse proxy

- Created a dedicated LXC for Nginx Proxy Manager.
- Installed Docker and Docker Compose.
- Deployed Nginx Proxy Manager.
- Began using friendly internal hostnames instead of memorizing IP:port combinations.

### Troubleshooting highlight
The LXC initially had only an IPv6 link-local address and no usable IPv4 route. Enabling IPv4 DHCP on `net0` fixed `Network is unreachable` and package-download failures.

## Phase 6 — Dashboard

- Deployed Homarr in a dedicated Docker-enabled LXC.
- Began integrating Proxmox and other services into a single dashboard.
- Worked through Proxmox API-token permissions and certificate trust.

## Phase 7 — Windows lab VM

- Created a Windows 11 VM.
- Loaded VirtIO storage drivers during installation.
- Installed the VM on a VirtIO-backed virtual disk.
- Used the VM as a Windows lab environment.

## Planned

- Uptime Kuma
- Immich
- Better backup strategy
- Cybersecurity monitoring / SIEM experiments
- More automation and infrastructure documentation

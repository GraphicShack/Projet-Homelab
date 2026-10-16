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

# Homarr

Homarr provides a centralized dashboard for homelab services.

It is deployed with Docker in a dedicated LXC and is intended to link to Proxmox, AdGuard Home, Nginx Proxy Manager, and future services.

## Proxmox integration

The integration uses a dedicated low-privilege API account/token rather than the root account.

A certificate-trust issue was encountered because Proxmox uses its own certificate authority by default. The correct approach is to trust the Proxmox CA rather than disabling TLS verification.

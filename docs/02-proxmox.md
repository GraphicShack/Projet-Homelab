# Proxmox VE

Proxmox VE is the base of the lab. It provides the web interface, LXC containers, virtual machines, storage management, and snapshots.

## Key configuration

- Hardware virtualization enabled in BIOS.
- Linux bridge used for guest networking.
- LXC used for lightweight always-on services.
- VMs used where a complete operating system is useful.

## Useful checks

```bash
ip -br link
ip -br addr
ip route
```

These commands quickly show whether the host has a working interface, address, and default route.

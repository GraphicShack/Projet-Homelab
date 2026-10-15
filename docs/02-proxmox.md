# Proxmox VE

Proxmox VE is the base of the lab. It provides the web interface, LXC containers, virtual machines, storage management, and snapshots.

## Key configuration

- Hardware virtualization enabled in BIOS.
- Linux bridge used for guest networking.
- LXC used for lightweight always-on services.
- VMs used where a complete operating system is useful.

## Problems encountered

### 1. Hardware virtualization was not enabled

**Symptom:** Proxmox reported that hardware-accelerated KVM was not available.

**What it meant:** The CPU supported virtualization, but Intel VT-x / VMX was disabled in BIOS. Without it, Proxmox cannot efficiently run full virtual machines.

**Fix:** Enabled Intel VMX / VT-x in the BIOS, then rebooted the host.

**Lesson learned:** Before troubleshooting Proxmox itself, verify that virtualization support is enabled at the motherboard/BIOS level.

### 2. Proxmox networking was initially confusing

**Symptom:** The web interface was difficult to reach after installation.

**What it meant:** Proxmox depends on a correct static IP, gateway, subnet, and bridge configuration. A wrong address or route can make the host look "down" even if Proxmox is running normally.

**Fix:** Verified the home gateway, subnet and management IP, then checked the bridge/interface configuration.

**Useful checks:**

```bash
ip -br link
ip -br addr
ip route
```

**Lesson learned:** When a host is unreachable, first check: interface state, IP address, then route.

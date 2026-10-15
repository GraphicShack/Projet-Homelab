# Troubleshooting Notes

This page summarizes the main failures encountered while building the homelab and the reasoning used to isolate them.

## 1. Proxmox: hardware-accelerated KVM unavailable

**Cause:** CPU virtualization was disabled in BIOS.

**Fix:** Enabled Intel VT-x / VMX.

**Diagnostic idea:** If virtualization features are unavailable before any VM even starts, check BIOS/firmware first.

---

## 2. LXC: `Network is unreachable`

**Symptoms:**

```text
ping: connect: Network is unreachable
Temporary failure resolving 'archive.ubuntu.com'
```

**Diagnosis:** `ip -br addr` showed no IPv4 address.

**Fix:** Enabled IPv4 DHCP for the LXC interface on `vmbr0`, then restarted the container.

**Key lesson:** Do not diagnose DNS before confirming basic IP connectivity.

---

## 3. Tailscale daemon would not start in LXC

**Cause:** Missing TUN device inside the unprivileged LXC.

**Fix:** Passed `/dev/net/tun` into the container and restarted it.

**Key lesson:** Containerized networking tools may need host kernel devices/capabilities.

---

## 4. Samba path/permission problems

**Causes:** Missing mount point, exFAT permission behavior, and LXC UID/GID remapping.

**Fix:** Verified mounts first, then aligned mount UID/GID options with the container user.

**Key lesson:** Separate "is the disk mounted?" from "does the user have permission?".

---

## 5. AdGuard Home running but not blocking anything

**Cause:** Clients were not actually using AdGuard as their DNS server.

**Fix:** Changed client/router DNS settings and verified queries appeared in the dashboard.

**Key lesson:** Zero telemetry often means the service is being bypassed rather than broken.

---

## 6. Windows installer could not see virtual disk

**Cause:** Missing VirtIO SCSI driver.

**Fix:** Loaded the VirtIO Windows driver during setup.

---

## 7. Homarr could reach Proxmox but rejected TLS

**Cause:** Proxmox certificate chained to a private Proxmox CA.

**Fix:** Imported/trusted the Proxmox root CA.

**Key lesson:** Prefer fixing trust properly instead of disabling certificate verification.

---

## 8. Homarr Proxmox integration authorization failure

**Cause:** API token permissions were insufficient or incorrectly scoped.

**Fix:** Used a dedicated API user/token with read-only `PVEAuditor` permissions.

---

## 9. Remote access: LAN addresses unavailable from another network

**Cause:** `192.168.x.x` addresses are private to the home LAN.

**Fix:** Used Tailscale addresses for direct remote access; subnet routing is the planned solution for reaching the full LAN.

---

## Troubleshooting workflow used repeatedly

1. Verify the service is running.
2. Verify the interface has an IP address.
3. Verify a default route exists.
4. Test a raw IP before testing DNS.
5. Verify the expected port is listening.
6. Verify firewall/ACL permissions.
7. Only then troubleshoot the application itself.

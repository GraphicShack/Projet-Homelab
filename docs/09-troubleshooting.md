# Troubleshooting Notes

## LXC: `Network is unreachable`

### Symptoms

```text
ping: connect: Network is unreachable
Temporary failure resolving 'archive.ubuntu.com'
```

### Diagnosis

`ip -br addr` showed only an IPv6 link-local address and no IPv4 address.

### Fix

Enabled IPv4 DHCP for the LXC network interface on the Proxmox bridge, then restarted the container.

---

## Tailscale daemon would not start in LXC

### Symptom

`tailscaled` failed inside an unprivileged LXC.

### Fix

Passed `/dev/net/tun` through to the container and restarted it.

---

## Windows installer could not see virtual disk

### Fix

Loaded the Windows VirtIO SCSI driver from the VirtIO driver ISO during installation.

---

## Homarr could reach Proxmox but rejected TLS

### Cause

The Proxmox API certificate chains to the Proxmox internal CA, which Homarr did not trust by default.

### Fix

Imported the Proxmox CA certificate into Homarr instead of disabling certificate validation.

# Nginx Proxy Manager

Nginx Proxy Manager provides a graphical interface for reverse-proxy configuration.

## Problems encountered

### 1. The LXC had no IPv4 connectivity

**Symptoms:**

```text
Temporary failure resolving 'archive.ubuntu.com'
ping: connect: Network is unreachable
```

**Diagnosis:** `ip -br addr` showed only an IPv6 link-local address (`fe80::...`) and no `192.168.x.x` IPv4 address.

**What it meant:** The container interface existed, but it had not received a usable IPv4 configuration or default route.

**Fix:** In Proxmox, edited `net0` and enabled IPv4 DHCP on bridge `vmbr0`, then restarted the container.

**Lesson learned:** A DNS error can actually be a lower-level network problem. Always test raw connectivity first.

### 2. Docker packages could not be downloaded

**Symptom:** `apt` could not locate or download Docker-related packages.

**What it meant:** This was a consequence of the missing network connectivity, not necessarily a Docker problem.

**Fix:** Repaired the LXC network first, reran `apt update`, then installed Docker and Docker Compose.

### 3. Friendly hostnames do not work automatically

**Symptom:** Creating a proxy host such as `adguard.home` did not automatically make the name resolvable.

**What it meant:** Nginx Proxy Manager routes HTTP requests after a hostname reaches it; DNS still has to map that hostname to the NPM server.

**Fix:** Used a hosts-file entry for testing and planned local DNS records for a cleaner long-term setup.

Example concept:

```text
service.example.internal
        -> DNS resolves to Nginx Proxy Manager
        -> Nginx Proxy Manager
        -> backend-service:port
```

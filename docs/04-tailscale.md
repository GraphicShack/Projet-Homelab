# Tailscale Remote Access

Tailscale provides private remote access without exposing SMB or management ports directly to the public Internet.

## Problems encountered

### 1. `tailscaled` would not start inside the LXC

**Symptom:** Tailscale installed successfully, but the service failed to start and the socket under `/run/tailscale/` was missing.

**What it meant:** Tailscale needs a TUN network device. In an LXC, that device is not always available automatically.

**Fix:** Passed `/dev/net/tun` from the Proxmox host into the container and restarted the service/container.

**Lesson learned:** A package being installed does not mean the underlying kernel/device requirements are available inside a container.

### 2. Remote Samba access required more than just installing Tailscale

**Symptom:** The Tailscale IP could be reached, but SMB access was still inconsistent.

**What it meant:** The file service also needed to listen on the correct interfaces and allow the Tailscale address range.

**Fix:** Verified that Samba listened on port 445 and adjusted Samba network allow rules as needed.

### 3. Local IP addresses do not automatically work from outside the home network

**Symptom:** Addresses such as `192.168.x.x` were unreachable from the university network.

**What it meant:** Private LAN addresses only exist inside the home network. Tailscale provides separate private addresses unless subnet routing is configured.

**Fix:** Used the server's Tailscale address for direct access. Subnet routing was identified as the next step for reaching the entire home LAN remotely.

> Real Tailscale IP addresses and authentication details are not published.

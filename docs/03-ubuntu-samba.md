# Ubuntu Server + Samba

The Ubuntu Server LXC acts as the main file server.

## Problems encountered

### 1. The shared `/data` directory did not exist

**Symptom:** A command such as `chown -R ... /data` returned "No such file or directory".

**What it meant:** The mount point expected inside the container had not yet been created or mounted correctly.

**Fix:** Created the directory, verified the host storage mount, then passed the storage into the LXC.

**Lesson learned:** Before changing permissions, verify that the path exists and that the expected disk is actually mounted there.

### 2. External exFAT storage permissions did not behave like a normal Linux filesystem

**Symptom:** File ownership and permissions did not behave as expected.

**What it meant:** exFAT does not store normal Linux ownership/permission metadata. Ownership must be defined at mount time using options such as `uid`, `gid`, and `umask`.

**Fix:** Mounted the drive with the correct UID/GID mapping for the unprivileged LXC.

**Lesson learned:** Filesystem type matters. Permission troubleshooting on exFAT is different from ext4/ZFS.

### 3. LXC UID/GID mapping caused access problems

**Symptom:** Files were visible but not writable from the Ubuntu container.

**What it meant:** An unprivileged LXC remaps container user IDs. For example, UID 1000 in the container is represented differently on the Proxmox host.

**Fix:** Adjusted the host mount options so the files appeared with the correct owner inside the LXC.

**Lesson learned:** When bind-mounting storage into unprivileged containers, think about both the host UID and the container UID.

### 4. Samba worked locally but remote/mobile access was inconsistent

**Symptom:** The share was accessible on the LAN but difficult to reach from another device or network.

**What it meant:** Samba itself was working; the remaining problem was network reachability rather than the file share configuration.

**Fix:** Verified that `smbd` was listening on TCP 445 and later used Tailscale for private remote access.

Example public-safe share definition:

```ini
[data]
    path = /data
    valid users = example-user
    browseable = yes
    writable = yes
    read only = no
    guest ok = no
```

Real usernames, passwords, and network ACLs are intentionally omitted from this repository.

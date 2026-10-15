# Ubuntu Server + Samba

The Ubuntu Server LXC acts as the main file server.

## Samba

Samba exposes Linux directories to Windows, macOS, and compatible mobile file managers using SMB.

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

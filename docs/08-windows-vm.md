# Windows 11 VM

A Windows 11 virtual machine is used as a Windows lab environment.

## Installation notes

The installer initially could not see the virtual disk. The VirtIO SCSI driver was loaded from the VirtIO driver ISO, after which the disk became available for installation.

## Planned improvements

- QEMU Guest Agent
- Remote Desktop / remote management
- Snapshots before lab changes

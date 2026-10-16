# Windows 11 VM

A Windows 11 virtual machine is used as a Windows lab environment.

## Problems encountered

### 1. The VM booted to UEFI instead of Windows Setup

**Symptom:** The VM showed the UEFI boot menu or PXE boot attempts.

**What it meant:** The VM was not booting from the Windows ISO first.

**Fix:** Selected the Windows DVD-ROM from the UEFI boot menu and corrected the VM boot order.

### 2. Windows Setup could not see the virtual disk

**Symptom:** The Windows installer showed an empty disk list.

**What it meant:** The disk used a VirtIO SCSI controller, but Windows did not include that driver by default.

**Fix:** Loaded the VirtIO SCSI driver from the VirtIO driver ISO:

```text
vioscsi -> w11 -> amd64
```

The virtual disk then appeared and installation could continue.

![Windows 11 VirtIO driver selection](../screenshots/windows11-virtio-storage-driver.jpg)

This is the driver-loading stage of Windows Setup. After the correct VirtIO storage driver was loaded, Windows could detect the virtual disk.

**Lesson learned:** VirtIO gives better virtualization performance, but Windows often needs the driver during installation.

### 3. Proxmox did not display the Windows IP address

**Symptom:** The Summary page showed that no Guest Agent was configured.

**What it meant:** Proxmox could not query the guest operating system for its network information.

**Workaround:** Ran `ipconfig` inside Windows.

**Planned fix:** Install and enable QEMU Guest Agent so Proxmox can display guest information directly.

![Sanitized Windows 11 VM](../screenshots/windows11-vm-running-redacted.jpg)

The VM was running correctly and had network connectivity. The username and network-identifying values are hidden in the public screenshot.

### 4. Memory usage reached 100%

**Symptom:** Proxmox showed the Windows VM using all assigned RAM.

**What it meant:** Windows can consume most assigned memory for active processes and caching; it also indicated that the VM might benefit from more memory depending on workload.

**Approach:** Monitor actual performance before increasing RAM, because high reported usage alone does not always mean the VM is unhealthy.

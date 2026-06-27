# Issues Encountered & Fixes — Phase 1

This log documents every significant issue encountered during Phase 1 setup, how it was diagnosed, and how it was resolved. This mirrors the kind of structured troubleshooting documentation expected in a real SOC environment.

---

## Issue 1 — pfSense ISO Not Recognized by VirtualBox

**Date:** June 1, 2026

### Problem
When attempting to attach the downloaded pfSense installer to the VM's optical drive in VirtualBox, the file was not visible in the file picker.

### Root Cause
The downloaded file from Netgate was compressed as `.iso.gz` — a Gzip-compressed ISO image. VirtualBox requires an uncompressed `.iso` file and could not read the compressed archive directly.

### Fix
Extracted the `.iso.gz` file using WinRAR (Extract Here), which produced a clean `.iso` file that VirtualBox recognized correctly.

### Lesson
Always verify the file extension of downloaded ISOs before attempting to attach them. Netgate and some other vendors distribute compressed images that require extraction before use.

---

## Issue 2 — pfSense Installer Used Online Installation Method

**Date:** June 1, 2026

### Problem
The Netgate installer v1.2-RELEASE used a network-based installation method, requiring active internet access during the install process to download pfSense CE packages.

### Root Cause
Newer Netgate installers are lightweight bootstrappers that fetch packages from Netgate's servers rather than including all files on the ISO.

### Fix
Ensured pfSense VM's WAN adapter was set to NAT before starting the installation, giving the VM internet access through the host machine. The installer successfully downloaded pfSense CE 2.8.1 (103 MB) during setup.

### Lesson
Check whether an installer is online or offline before beginning. For offline lab environments, an offline ISO or pre-downloaded packages would be required.

---

## Issue 3 — VM-to-VM Connectivity Failure (Host-Only Networking)

**Date:** June 15, 2026

### Problem
After deploying all three VMs on the VirtualBox Host-Only network (`192.168.100.0/24`), VM-to-VM ping tests failed with 100% packet loss in all directions. The Windows host could reach pfSense (`192.168.100.1`) successfully, but could not reach SOC-Monitor (`192.168.100.10`).

### Diagnosis Steps

1. **Verified network adapter settings** — both VMs confirmed on the same Host-Only adapter (`VirtualBox Host-Only Ethernet Adapter`)
2. **Confirmed IP configuration on Ubuntu** — `ip addr show enp0s3` showed `192.168.100.10/24` correctly assigned
3. **Confirmed interface state** — `ip link show enp0s3` showed `state UP`
4. **Checked pfSense LAN interface** — `ifconfig em1` showed `inet 192.168.100.1`, `status: active`
5. **ARP table check** — `ip neigh show` on SOC-Monitor revealed:
   ```
   192.168.100.1 dev enp0s3 lladdr 0a:00:27:00:00:04 STALE
   ```
   This confirmed Layer 2 (MAC) resolution was working — pfSense's MAC address was known — but ICMP packets were not getting through.
6. **Disabled pfSense firewall temporarily** — `pfctl -d` on pfSense shell — ping still failed, ruling out firewall rules as the cause
7. **Tested from Windows host** — host could ping `192.168.100.1` but not `192.168.100.10`, confirming the issue was specific to SOC-Monitor's network path

### Root Cause
VirtualBox 7.2.x on Windows 11 has known issues with VM-to-VM traffic on Host-Only networks when **Promiscuous Mode** is set to `Deny` (the default). With Deny mode, the virtual switch filters out frames destined for MAC addresses other than the VM's own, effectively blocking inter-VM traffic.

### Fix
Two changes were required:

**Step 1:** Changed both VM adapters' **Promiscuous Mode** from `Deny` to `Allow All`

**Step 2:** Changed both VM adapters' network type from **Host-Only** to **Internal Network** (named `soclab`)

Internal Network mode creates a pure VM-to-VM virtual switch with no Windows host involvement, completely eliminating host driver interference. This resolved connectivity immediately.

### Verification
After the fix:
```
ping -c 4 192.168.100.1  →  4/4 received, 0% packet loss ✅
ping -c 4 8.8.8.8        →  4/4 received, 0% packet loss ✅
```

### Lesson
On VirtualBox 7.x with Windows 11:
- Always set Promiscuous Mode to **Allow All** on lab VM adapters
- **Internal Network** is more reliable than Host-Only for VM-to-VM communication in isolated lab environments
- ARP table inspection (`ip neigh show`) is a valuable first diagnostic step for Layer 2/3 connectivity issues

---

## Issue 4 — Ubuntu Mirror Unreachable During Installation

**Date:** June 6, 2026

### Problem
During Ubuntu Server installation, the archive mirror configuration screen reported failure to reach `archive.ubuntu.com`, showing repeated `Temporary failure resolving 'archive.ubuntu.com'` errors.

### Root Cause
The Ubuntu VM was being installed on the Host-Only/Internal network which had no internet access at that stage of the installation (pfSense was not yet fully configured as a gateway).

### Fix
Skipped the mirror check by selecting **Done** and continued the installation. The installer fell back to local ISO packages for the base installation, completing successfully without the online mirror.

Post-installation internet access was confirmed once pfSense was running and routing was established.

### Lesson
Base Ubuntu Server installation does not require internet access — the ISO contains sufficient packages for the core OS. Online mirrors are only needed for additional packages and updates post-install.

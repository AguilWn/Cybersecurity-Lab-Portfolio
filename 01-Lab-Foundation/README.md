# 01 — SOC Home Lab Foundation

> **Project:** Secure Virtual SOC Lab  
> **Phase:** 1 of 5 — Lab Foundation  
> **Status:** ✅ Complete  
> **Platform:** Windows 11 Host — Oracle VirtualBox 7.2.8

---

## Objective

Design and deploy a fully isolated, virtualized SOC lab environment from scratch. This phase establishes the core network infrastructure that all future phases — SIEM deployment, log monitoring, attack simulation, and incident response — will build upon.

The goal was to replicate a realistic SOC network topology using enterprise-grade open-source tools, demonstrating practical skills in virtualization, network segmentation, Linux server administration, and secure remote access.

---

## Lab Architecture

```
[ Windows 11 Host — ASUS TUF, i5-8300H, 16GB RAM ]
                        |
              [ VirtualBox 7.2.8 ]
                        |
        [ Internal Network: "soclab" — 192.168.100.0/24 ]
                        |
              [ pfSense CE 2.8.1 ]
              WAN: NAT → Internet
              LAN: 192.168.100.1
                    /           \
                   /             \
      [ SOC-Monitor ]        [ Kali-Analyst ]
      Ubuntu 26.04 LTS        Kali Linux
      192.168.100.10          192.168.100.20
      SIEM / Log Server       Analyst Workstation
```

---

## Virtual Machines

| VM Name | OS | IP Address | Role |
|---|---|---|---|
| pfSense-Lab | pfSense CE 2.8.1 | 192.168.100.1 | Firewall / Router / Gateway |
| SOC-Monitor | Ubuntu Server 26.04 LTS | 192.168.100.10 | SIEM Node / Log Server |
| Kali-Analyst | Kali Linux | 192.168.100.20 | Analyst Workstation |

---

## Tools Used

| Tool | Version | Purpose |
|---|---|---|
| Oracle VirtualBox | 7.2.8 | Hypervisor |
| VirtualBox Extension Pack | 7.2.8 | USB, RDP, and NVMe support |
| pfSense CE | 2.8.1 | Firewall, router, and NAT gateway |
| Ubuntu Server | 26.04 LTS | SIEM and monitoring server OS |
| Kali Linux | Latest stable | Security analyst workstation OS |

---

## Network Design Decisions

### Internal Network vs Host-Only

After testing, the lab was configured using VirtualBox **Internal Network** mode (named `soclab`) rather than Host-Only networking. Internal Network creates a fully isolated virtual switch between VMs with no Windows host involvement, which:

- Eliminates Windows driver interference with VM-to-VM traffic
- More accurately replicates a real isolated SOC network segment
- Prevents accidental traffic leakage to the host machine

All VM adapters were set to **Promiscuous Mode: Allow All** to ensure full VM-to-VM traffic visibility across the virtual switch.

### Static IP Assignment

All VMs were assigned static IPs rather than using DHCP. This mirrors real SOC environments where servers and infrastructure nodes have fixed, predictable addresses — essential for log correlation, firewall rules, and monitoring configuration.

| Device | IP | Reason |
|---|---|---|
| pfSense LAN | 192.168.100.1 | Default gateway for all lab VMs |
| SOC-Monitor | 192.168.100.10 | Fixed address for SIEM and SSH target |
| Kali-Analyst | 192.168.100.20 | Fixed address for analyst workstation |

---

## Step-by-Step Setup Summary

### 1. Hypervisor Installation
- Installed Oracle VirtualBox 7.2.8 with Extension Pack on Windows 11 host
- Configured VirtualBox Internal Network `soclab` as the lab's isolated virtual switch

### 2. pfSense Firewall Deployment
- Deployed pfSense CE 2.8.1 as the lab's firewall and default gateway
- Configured dual network adapters:
  - **Adapter 1 (WAN):** NAT — provides internet access via host machine
  - **Adapter 2 (LAN):** Internal Network `soclab` — connects to lab VMs
- Set LAN IP to `192.168.100.1/24`
- Verified firewall rules allowing LAN-to-any traffic

### 3. Ubuntu Server Deployment (SOC-Monitor)
- Deployed Ubuntu Server 26.04 LTS as the SIEM and monitoring node
- Configured static IP `192.168.100.10/24` via installer network configuration
- Created administrator account: `socadmin` on hostname `soc-monitor`
- Enabled OpenSSH server during installation for remote management
- Confirmed internet routing through pfSense NAT

### 4. Kali Linux Deployment (Kali-Analyst)
- Imported pre-built Kali Linux VirtualBox image as the analyst workstation
- Configured network adapter to Internal Network `soclab`
- Set static IP `192.168.100.20/24` via `/etc/network/interfaces`
- Verified full connectivity to all lab nodes

### 5. Connectivity Validation
- Confirmed bidirectional ICMP connectivity between all VMs
- Confirmed internet access through pfSense NAT from all VMs
- Established SSH session from Kali-Analyst to SOC-Monitor

---

## SSH Configuration

OpenSSH was enabled on SOC-Monitor during the Ubuntu Server installation to allow secure remote management from the Kali-Analyst workstation. This mirrors real-world SOC environments where Linux servers are managed remotely via SSH rather than direct console access.

**Configuration applied:**
- OpenSSH server: Enabled
- Password authentication: Enabled (for initial lab access)
- Port: 22 (default)

> **Note:** SSH hardening — including disabling password authentication in favour of key-based auth and restricting access by user and IP — is covered in Phase 2: Linux Hardening.

SSH access was verified by successfully connecting from Kali-Analyst:

```bash
ssh socadmin@192.168.100.10
```

---

## Connectivity Validation Results

| Test | Command | Result |
|---|---|---|
| SOC-Monitor → pfSense | `ping -c 4 192.168.100.1` | ✅ 0% packet loss |
| SOC-Monitor → Internet | `ping -c 4 8.8.8.8` | ✅ 0% packet loss |
| Kali → pfSense | `ping -c 4 192.168.100.1` | ✅ 0% packet loss |
| Kali → SOC-Monitor | `ping -c 4 192.168.100.10` | ✅ 0% packet loss |
| Kali → Internet | `ping -c 4 8.8.8.8` | ✅ 0% packet loss |
| SSH Kali → SOC-Monitor | `ssh socadmin@192.168.100.10` | ✅ Successful login |

---

## Screenshots

| Screenshot | Description |
|---|---|
| `09-pfsense-console-wan-lan.png` | pfSense console showing WAN and LAN interface IPs |
| `11-ubuntu-network-config.png` | Ubuntu installer static IP configuration |
| `12-ubuntu-profile-config.png` | Ubuntu server profile — hostname and user creation |
| `15-soc-monitor-login-success.png` | First successful login to SOC-Monitor |
| `15-kali-analyst-network-settings.png` | Kali-Analyst VirtualBox network adapter settings |
| `18-pfsense-internal-network-soclab.png` | pfSense adapter configured on Internal Network soclab |
| `19-soc-monitor-internal-network-soclab.png` | SOC-Monitor adapter configured on Internal Network soclab |
| `20-kali-ip-addr-verify.png` | Kali static IP verified at 192.168.100.20 |
| `21-kali-connectivity-test.png` | Kali pinging pfSense, SOC-Monitor, and internet |
| `22-kali-ssh-to-soc-monitor.png` | SSH session from Kali-Analyst to SOC-Monitor |

---

## Issues Encountered

See [`issues-and-fixes.md`](./issues-and-fixes.md) for the full troubleshooting log.

**Summary of key issues:**
- pfSense ISO was compressed as `.iso.gz` — required extraction before use
- VM-to-VM connectivity failed on Host-Only networking — resolved by switching to Internal Network mode with Promiscuous Mode set to Allow All
- pfSense online installer required internet access during installation — provided via NAT adapter on WAN interface

---

## Lessons Learned

See [`lessons-learned.md`](./lessons-learned.md) for the full reflection.

**Key takeaways:**
- VirtualBox Internal Network provides more reliable VM-to-VM connectivity than Host-Only on Windows 11
- Promiscuous Mode must be explicitly set to Allow All for VM-to-VM traffic on virtual switches
- Static IP assignment is essential for a stable, predictable lab environment
- pfSense CE is a capable enterprise-grade firewall suitable for realistic SOC lab simulation

---

## SOC Analyst Skills Demonstrated

| Skill | How It Was Applied |
|---|---|
| Network design | Designed isolated lab topology with segmented subnets and a dedicated firewall |
| Virtualization | Deployed and configured multiple VMs with custom network adapters |
| Linux administration | Static IP configuration, user creation, SSH setup, service management |
| Firewall concepts | Configured pfSense as a segmented gateway with NAT and LAN rules |
| Troubleshooting | Methodically diagnosed and resolved VM-to-VM connectivity issues using ARP, ping, and firewall rule analysis |
| SSH / Remote access | Established secure remote management between analyst workstation and server |
| Documentation | Produced structured, evidence-backed lab documentation |

---

## Next Phase

→ [02 — Linux Hardening](../02-Linux-Hardening/README.md)

In Phase 2 we harden the SOC-Monitor Ubuntu server — implementing SSH key authentication, disabling unnecessary services, configuring UFW firewall rules, setting up audit logging, and establishing a security baseline.

# Lab Topology — Phase 1

## Network Overview

| Property | Value |
|---|---|
| Network Name | soclab |
| Network Type | VirtualBox Internal Network |
| Subnet | 192.168.100.0/24 |
| Gateway | 192.168.100.1 (pfSense LAN) |
| DNS | 8.8.8.8 (Google) |
| DHCP | Disabled — all IPs static |
| Internet Access | Via pfSense WAN (NAT) |

---

## Network Diagram

```
                    [ INTERNET ]
                         |
                         | (NAT)
                         |
              ┌──────────────────────┐
              │     pfSense-Lab      │
              │   pfSense CE 2.8.1   │
              │                      │
              │  WAN (em0): NAT      │
              │  LAN (em1):          │
              │  192.168.100.1/24    │
              └──────────┬───────────┘
                         │
          ───────────────────────────────
          │    Internal Network: soclab  │
          │    192.168.100.0/24          │
          ───────────────────────────────
                    │           │
        ┌───────────┘           └───────────┐
        │                                   │
┌───────────────┐                 ┌──────────────────┐
│  SOC-Monitor  │                 │   Kali-Analyst   │
│ Ubuntu 26.04  │                 │   Kali Linux     │
│               │                 │                  │
│ 192.168.100.10│                 │ 192.168.100.20   │
│ SIEM / Server │                 │ Analyst WS       │
└───────────────┘                 └──────────────────┘
        │                                   │
        └──────── SSH (Port 22) ────────────┘
                 Kali → SOC-Monitor
```

---

## VM Specifications

### pfSense-Lab

| Property | Value |
|---|---|
| OS | pfSense CE 2.8.1 (FreeBSD) |
| RAM | 512 MB |
| CPU | 1 core |
| Disk | 8 GB |
| Adapter 1 | NAT (WAN — internet access) |
| Adapter 2 | Internal Network soclab (LAN — 192.168.100.1) |
| Promiscuous Mode | Allow All |
| Role | Firewall, router, NAT gateway, default gateway |

### SOC-Monitor

| Property | Value |
|---|---|
| OS | Ubuntu Server 26.04 LTS |
| RAM | 4096 MB |
| CPU | 2 cores |
| Disk | 50 GB |
| Adapter 1 | Internal Network soclab (192.168.100.10) |
| Promiscuous Mode | Allow All |
| Hostname | soc-monitor |
| User | socadmin |
| SSH | Enabled (port 22) |
| Role | SIEM node, log server, monitoring platform |

### Kali-Analyst

| Property | Value |
|---|---|
| OS | Kali Linux (latest stable) |
| RAM | 2048 MB |
| CPU | 2 cores |
| Disk | Pre-built VDI |
| Adapter 1 | Internal Network soclab (192.168.100.20) |
| Promiscuous Mode | Allow All |
| User | kali |
| Role | Analyst workstation, attack simulation, SSH client |

---

## Traffic Flow

### VM-to-VM Communication
All VMs communicate directly through the `soclab` Internal Network virtual switch. Traffic stays entirely within VirtualBox and never touches the Windows host network stack.

### Internet Access
VMs access the internet through pfSense's WAN NAT adapter. Traffic flow:

```
VM → 192.168.100.1 (pfSense LAN) → NAT → Host Network → Internet
```

### SSH Management
The Kali-Analyst workstation manages SOC-Monitor via SSH:

```
Kali (192.168.100.20) → SSH Port 22 → SOC-Monitor (192.168.100.10)
```

---

## Port Reference

| Port | Protocol | Service | VM |
|---|---|---|---|
| 22 | TCP | OpenSSH | SOC-Monitor |
| 80 | TCP | pfSense Web GUI (HTTP) | pfSense-Lab |
| 443 | TCP | pfSense Web GUI (HTTPS) | pfSense-Lab |
| 1514 | UDP | Wazuh Agent (Phase 3) | SOC-Monitor |
| 1515 | TCP | Wazuh Registration (Phase 3) | SOC-Monitor |

> Ports marked Phase 3 are planned for the SIEM deployment phase and are not yet active.

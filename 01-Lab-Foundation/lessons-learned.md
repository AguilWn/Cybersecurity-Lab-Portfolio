# Lessons Learned — Phase 1

## Overview

Phase 1 was the most foundational phase of the project — building the virtual lab environment from scratch. While the technical steps were straightforward in theory, the real learning came from troubleshooting unexpected issues and making informed decisions about network design. This document captures the key takeaways.

---

## 1. Network Architecture Decisions Matter Early

The choice of VirtualBox network mode (Host-Only vs Internal Network) had a significant impact on lab stability. What appeared to be a minor configuration detail turned out to be the root cause of hours of connectivity troubleshooting.

**Takeaway:** In both virtual and physical network environments, understanding the difference between network isolation modes is critical before deployment. Internal Network provided a cleaner, more realistic isolated topology — and it should be the default choice for SOC lab environments going forward.

---

## 2. Methodical Troubleshooting Saves Time

When VM-to-VM connectivity failed, the temptation was to reinstall or reconfigure randomly. Instead, a structured diagnostic approach was used:

- Start at Layer 1 (is the cable connected?)
- Move to Layer 2 (is ARP resolving?)
- Move to Layer 3 (is ICMP getting through?)
- Check the firewall (are rules blocking traffic?)
- Check the hypervisor (is the virtual switch filtering frames?)

This OSI-layer-by-layer approach pinpointed the exact cause efficiently — ARP was resolving (Layer 2 worked) but ICMP was failing (Layer 3 blocked), which pointed to Promiscuous Mode filtering at the hypervisor level.

**Takeaway:** The same structured methodology applies to real SOC incident investigation. Start with what you know, test each layer systematically, and document every step.

---

## 3. Static IPs Are Essential for Lab Environments

Assigning static IPs to all VMs — rather than relying on DHCP — made the lab significantly easier to manage. Every ping test, SSH command, and firewall rule used predictable, fixed addresses.

**Takeaway:** In production SOC environments, critical infrastructure (SIEM servers, firewalls, log collectors) always have fixed IPs. This lab reflects that best practice from day one.

---

## 4. Documentation Should Be Written Alongside the Work

The most accurate documentation came from notes taken during the build — including error messages, failed attempts, and commands that didn't work. Trying to reconstruct documentation after the fact loses detail.

**Takeaway:** In a SOC role, incident timelines and investigation notes need to be recorded in real time, not reconstructed from memory. Building this habit during the lab phase directly translates to a professional workflow.

---

## 5. pfSense Is a Realistic Enterprise Tool

Using pfSense CE as the lab firewall rather than a simpler solution (like a basic VirtualBox NAT network) added meaningful complexity — and meaningful learning. Concepts like WAN/LAN interface assignment, NAT, firewall rules, and promiscuous mode are directly applicable to enterprise firewall management.

**Takeaway:** Choosing more realistic tools in a home lab, even when simpler alternatives exist, produces more transferable skills.

---

## 6. Pre-Built VM Images Save Time Without Sacrificing Learning

Importing Kali Linux from a pre-built VirtualBox VDI rather than installing from an ISO saved significant time without skipping any meaningful learning. The static IP configuration, network adapter setup, and connectivity testing still required hands-on work.

**Takeaway:** Use pre-built images where appropriate to avoid spending time on routine OS installation steps. Allocate that time to more meaningful security configuration and testing instead.

---

## Summary

| Area | Key Lesson |
|---|---|
| Networking | Internal Network > Host-Only for isolated lab environments |
| Troubleshooting | Layer-by-layer OSI diagnosis is always the right approach |
| Configuration | Static IPs are non-negotiable for stable lab infrastructure |
| Documentation | Write notes in real time — accuracy degrades quickly |
| Tool selection | Choose realistic tools even when simpler options exist |
| Efficiency | Pre-built images are a legitimate time-saving strategy |

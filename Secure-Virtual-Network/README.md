*[Project Title: Building a Secure Virtualized Network Environment]*

**Author:** Wennymar Aguil

**Date:** January 2026

**Category:** Virtualization / Systems Administration / Security Foundations

**1. Executive Summary**
The goal of this project was to design and deploy a controlled laboratory environment using Oracle VM VirtualBox. This lab serves as a secure "sandbox" to practice network monitoring, patch management, and security testing without risking the host machine's integrity. By configuring a Linux-based server (Ubuntu) alongside a Windows client, I established a cross-platform environment for analyzing system telemetry.

**2. Tools & Technologies**
**Hypervisor:** Oracle VM VirtualBox

**Operating Systems:** * Ubuntu Server/Desktop (Linux)

Windows 10/11 (Evaluation ISO)

**Networking:** VirtualBox NAT Network (Isolated from Host)

**Documentation:** Draw.io (Network Diagrams), Markdown


**6. Lessons Learned And Challenges**

**📅 Activity Log: January 18, 2026**
**Technical Challenge**: VirtualBox Installation Failure (Fatal Error)
During the initial deployment of the lab environment, the Oracle VirtualBox 7.2.4 installer failed with a generic "Fatal error during installation" message.

**Investigation & Troubleshooting Steps:**

**Root Cause Analysis**: I initially attempted to install the software on a secondary storage drive. On the second attempt, the installer explicitly flagged an "Invalid Installation Directory" error, citing unmet security requirements. This confirmed that VirtualBox 7.x requires specific directory permissions typically found only on the primary system drive (C:).

**System Cleanup**: To ensure a clean state, I utilized Revo Uninstaller to remove the partial installation. This was critical for purging orphaned registry entries and leftover system files that could conflict with a fresh install.

**Environmental Prep:** Performed a full system reboot to clear any locked system processes related to the virtual network drivers.

**Final Solution:** The third installation attempt was successful. The resolution involved:

Reverting the installation path to the default directory: C:\Program Files\Oracle\VirtualBox.

Running the installer with elevated Administrative privileges to ensure proper driver registration.

**Key Takeaways for SOC Documentation:**

**Standardize Installation Paths:** Core hypervisor software should be installed on the primary OS drive to satisfy security permission requirements, though Virtual Machine images (.vdi) can be safely stored on secondary drives later.

**Log Analysis:** When an installer fails, always refer to the MSI or VirtualBox installation logs to move beyond generic error messages and find the specific root cause.

**Clean State Recovery:** Utilizing advanced uninstallation tools like [Revo Uninstaller](https://www.revouninstaller.com/) is a "best practice" when a failed installation leaves behind registry "noise" that might block future attempts.


**📅 Activity Log: January 28, 2026**
**Technical Phase:** Virtual Machine Provisioning & Path Optimization
After resolving the initial hypervisor installation issues, I proceeded with provisioning the primary Linux node for the SOC lab. This phase involved custom hardware allocation and correcting default directory behaviors to align with best practices for storage management.

**1. Virtual Machine Configuration (Ubuntu-SOC-Lab)** Using the Oracle VirtualBox "New Virtual Machine" wizard, I provisioned the initial server node with the following specifications to ensure a balance between performance and host system stability:

**Virtual Machine Name:** Ubuntu-SOC-Lab

**Operating System:** Ubuntu 25.10 (64-bit) Server Edition

**Memory Allocation:** 2048 MB (2GB RAM)

**Processor Core Count:** 2 Virtual CPUs

**Storage:** 25.00 GB Virtual Hard Disk (VDI)

**ISO Image:** ubuntu-25.10-live-server-amd64.iso

**2. Storage Architecture & Path Redirection (Troubleshooting)**

**Challenge:** During the initial setup, I observed that the "Network" management options were not properly visible/accessible due to the default VirtualBox configuration pointing to an restricted system path.

**Action:** I accessed the Global Preferences and redirected the Default Machine Folder to my secondary storage: D:\Virtual Machines.

**Result:** This resolved the visibility issue and ensured that all future high-volume data (Virtual Hard Disks) is stored on the D: drive, preventing the C: system drive from reaching capacity during lab expansion.

**3. Initial Network State**

**Current Status:** As seen in the UI overview, the VM is currently attached to a standard NAT adapter (Intel PRO/1000 MT Desktop).

_Next Milestone:_ The next step is to transition this from a simple NAT interface to a dedicated NAT Network to allow for multi-node communication between the Ubuntu server and future Windows clients.

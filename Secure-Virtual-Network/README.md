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

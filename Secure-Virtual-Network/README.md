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

* January 18, 2026: During the VirtualBox installation, I encountered a dependency warning regarding Python Core packages. After researching, I determined these were only necessary for Python automation bindings. To maintain a lean lab environment, I proceeded with the standard installation, as the core hypervisor functions were not affected.

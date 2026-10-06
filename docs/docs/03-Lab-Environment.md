# Lab Environment

## Table of Contents

- [Overview](#overview)
- [Lab Objectives](#lab-objectives)
- [Host Machine Specifications](#host-machine-specifications)
- [Virtualization Platform](#virtualization-platform)
- [Virtual Machine Configuration](#virtual-machine-configuration)
- [Operating System](#operating-system)
- [Network Configuration](#network-configuration)
- [Software and Tools](#software-and-tools)
- [Lab Design Considerations](#lab-design-considerations)
- [Environment Limitations](#environment-limitations)
- [Next Steps](#next-steps)

---

# Overview

Before deploying Windows Server 2025, I planned the lab environment to ensure it could support the required server roles while remaining within the hardware limitations of my personal computer.

Rather than using enterprise hardware, I built this environment on a Windows 11 host computer running VMware Workstation. Although the hardware resources were limited, careful planning allowed me to deploy and test multiple Windows Server features successfully.

This section documents the hardware, software, and virtualization platform used throughout the project.

---

# Lab Objectives

The lab environment was designed to:

- Simulate a real-world Windows Server deployment.
- Provide a safe environment for learning and experimentation.
- Practice enterprise systems administration.
- Test Windows Server roles without affecting a production environment.
- Build practical troubleshooting experience.
- Document every stage of the deployment for future reference.

---

# Host Machine Specifications

The Windows Server virtual machine was hosted on my personal computer.

| Component | Specification |
|-----------|---------------|
| Host Operating System | Windows 11 |
| Processor | Intel Core i5 |
| CPU Cores | 4 |
| Memory (RAM) | 8 GB |
| Storage | 256 GB SSD |

Because the host system has limited memory, I carefully allocated resources to ensure both Windows 11 and the virtual machine remained stable throughout the deployment.

---

# Virtualization Platform

The project was built using VMware Workstation.

VMware Workstation provides a virtual environment that allows multiple operating systems to run simultaneously on a single physical computer. This makes it possible to build enterprise infrastructure without requiring dedicated hardware.

Using virtualization also allows snapshots, isolated networking, and safe testing before making configuration changes.

---

# Virtual Machine Configuration

The Windows Server virtual machine was configured with dedicated hardware resources appropriate for the host system.

| Component | Configuration |
|-----------|--------------|
| Virtual Machine | Windows Server 2025 |
| Hypervisor | VMware Workstation |
| Virtual CPU | Based on available host resources |
| Memory Allocation | Optimized for an 8 GB host system |
| Virtual Disk | Created during VM deployment |
| Network Adapter | Configured for lab networking |

The virtual machine configuration balanced performance with the available hardware resources to maintain a stable learning environment.

---

# Operating System

The server operating system deployed throughout this project was:

| Item | Value |
|------|-------|
| Operating System | Windows Server 2025 Evaluation |
| Installation Method | ISO Image |
| Deployment Type | Virtual Machine |

Windows Server 2025 provides the core services required to build an enterprise Active Directory environment, including identity management, networking, storage, and security.

---

# Network Configuration

The virtual network was configured to support communication between the Windows Server virtual machine and the surrounding lab environment.

The networking configuration allows the server to provide services such as:

- Active Directory Domain Services (AD DS)
- DNS
- DHCP
- File Sharing
- Remote Administration

Detailed IP addressing and network architecture are covered in the next documentation section.

---

# Software and Tools

The following software and administrative tools were used throughout this project.

| Software | Purpose |
|----------|---------|
| VMware Workstation | Virtualization platform |
| Windows Server 2025 | Server operating system |
| Server Manager | Windows Server administration |
| PowerShell | Command-line administration |
| Active Directory Users and Computers | Identity management |
| DNS Manager | DNS administration |
| DHCP Manager | DHCP administration |
| Group Policy Management | Policy administration |
| Windows Server Backup | Backup and recovery |
| Event Viewer | Monitoring and troubleshooting |

---

# Lab Design Considerations

While planning the environment, I considered several factors to ensure the deployment remained stable.

These included:

- Available system memory.
- Processor limitations.
- Storage capacity.
- Virtual machine performance.
- Future expansion.
- Ease of troubleshooting.
- Documentation quality.

Careful planning made it possible to complete the deployment despite the hardware limitations.

---

# Environment Limitations

Because this project was completed as a home lab, several enterprise technologies were intentionally excluded.

These include:

- Microsoft Entra ID
- Microsoft Intune
- Hyper-V
- Failover Clustering
- Multiple Domain Controllers
- Enterprise SAN Storage

Although these technologies were outside the scope of this project, they represent logical next steps as the lab continues to evolve.

---

# Next Steps

With the lab environment prepared, the next stage is to examine the network architecture that supports communication between the server and client systems.

➡️ Continue to **04-Network-Architecture.md**

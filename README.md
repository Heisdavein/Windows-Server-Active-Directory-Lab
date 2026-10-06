# 🖥️ Windows Server 2025 Enterprise Home Lab

![Windows Server](https://img.shields.io/badge/Windows%20Server-2025-0078D6?style=for-the-badge&logo=windows)
![VMware](https://img.shields.io/badge/VMware-Workstation-607078?style=for-the-badge&logo=vmware)
![Active Directory](https://img.shields.io/badge/Active%20Directory-DS-003366?style=for-the-badge)
![PowerShell](https://img.shields.io/badge/PowerShell-5.1-5391FE?style=for-the-badge&logo=powershell)
![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)

---

## Enterprise Windows Server Infrastructure Built from Scratch

This repository documents how I designed, deployed, configured, secured, and tested a complete Windows Server 2025 enterprise home lab using VMware Workstation.

Rather than following isolated tutorials, I built the environment as a complete infrastructure project. Every service was configured in the same order it would typically be deployed in a real organisation, allowing me to understand not only **how** each technology works, but **why** it is implemented that way.

Throughout the project, I documented every configuration, decision, verification step, and troubleshooting process to create a portfolio that reflects real-world systems administration practices.

---

# Project Overview

This project includes the complete deployment of:

- Windows Server 2025
- VMware Workstation
- Active Directory Domain Services (AD DS)
- DNS Server
- DHCP Server
- Group Policy
- File Server
- NTFS Permissions
- Storage Spaces
- Windows Defender
- Windows Defender Firewall
- Windows Server Backup
- Event Viewer
- Disaster Recovery Testing

Every section has been documented in detail and organised into individual documents for easier navigation.

---

# Business Scenario

Imagine a growing company that needs a secure and centrally managed Windows infrastructure.

The company requires:

- Centralised user authentication
- Automatic IP address management
- Name resolution
- Centralised policy management
- Shared file storage
- Backup and recovery
- Endpoint security
- Infrastructure monitoring

To meet those requirements, I built a Windows Server environment that closely mirrors what many organisations use in production while remaining safe to deploy inside a home lab.

---

# Project Objectives

The primary goals of this project were to:

- Build a complete Windows Server environment from scratch
- Gain practical Active Directory administration experience
- Configure core Windows networking services
- Learn enterprise system administration
- Practice infrastructure troubleshooting
- Develop professional documentation
- Create a portfolio project that demonstrates real-world IT skills

---

# Skills Demonstrated

## Windows Administration

- Windows Server Installation
- Server Configuration
- Remote Desktop
- Server Manager
- SConfig

## Identity Management

- Active Directory Domain Services
- Organizational Units
- User Administration
- Security Groups
- Domain Join
- FSMO Roles

## Networking

- DNS
- DHCP
- Static IP Configuration
- Reverse Lookup Zones
- Forward Lookup Zones
- A Records
- PTR Records
- CNAME Records

## Security

- Windows Defender
- Windows Defender Firewall
- Group Policy
- NTFS Permissions

## Storage

- File Server
- Storage Spaces
- Virtual Disks
- Storage Pools

## Backup & Recovery

- Windows Server Backup
- Backup Scheduling
- File Recovery
- Disaster Recovery

## Monitoring

- Event Viewer
- System Logs
- Administrative Logs

## Virtualization

- VMware Workstation
- Virtual Machines
- Snapshots

---

# Technologies Used

| Technology | Purpose |
|------------|----------|
| Windows Server 2025 | Server Operating System |
| VMware Workstation | Virtualization Platform |
| Active Directory | Identity Management |
| DNS | Name Resolution |
| DHCP | Automatic IP Address Assignment |
| Group Policy | Centralized Management |
| PowerShell | Administration |
| Windows Defender | Endpoint Protection |
| Windows Server Backup | Backup Solution |

---

# Lab Environment

| Component | Specification |
|------------|--------------|
| Host OS | Windows 11 |
| CPU | Intel Core i5 (4 Cores) |
| RAM | 8 GB |
| Storage | 256 GB SSD |
| Hypervisor | VMware Workstation |
| Server OS | Windows Server 2025 Evaluation |

---

# Repository Structure

```text
windows-server-2025-enterprise-homelab/

README.md

docs/

diagrams/

screenshots/

scripts/

assets/
```

---

# Documentation

| Document | Description |
|-----------|-------------|
| Project Overview | Project goals and scope |
| VMware Setup | Creating the virtual infrastructure |
| Windows Installation | Installing Windows Server |
| Active Directory | Domain Services deployment |
| DNS | DNS configuration |
| DHCP | DHCP deployment |
| Group Policy | GPO creation and management |
| File Server | Shared folders and NTFS permissions |
| Storage Spaces | Storage pools and virtual disks |
| Security | Defender and Firewall |
| Backup | Windows Server Backup |
| Monitoring | Event Viewer |
| Disaster Recovery | Backup restoration testing |
| Troubleshooting | Problems encountered and resolutions |
| Lessons Learned | Key takeaways |

---

# Architecture

> 📌 Architecture diagram coming soon.

---

# Screenshots

This repository contains screenshots demonstrating every major stage of the deployment.

Examples include:

- VMware Configuration
- Windows Installation
- Active Directory
- DNS Manager
- DHCP Manager
- Group Policy
- File Server
- Storage Spaces
- Windows Defender
- Firewall Rules
- Backup Jobs
- Event Viewer
- Recovery Testing

---

# Validation

Every configuration completed during this project was verified through testing.

Examples include:

- Domain authentication
- DNS name resolution
- DHCP lease assignment
- Group Policy updates
- Shared folder access
- Backup verification
- Restore testing

---

# Lessons Learned

This project strengthened my understanding of enterprise Windows infrastructure by teaching me how multiple Windows Server roles work together to provide authentication, networking, storage, security, and centralized management.

More importantly, it improved my troubleshooting skills, documentation practices, and confidence working with Windows Server technologies in a structured environment.

---

# Future Improvements

Future enhancements include:

- Microsoft Entra ID
- Microsoft Intune
- Hybrid Identity
- Azure AD Connect
- Certificate Services
- WSUS
- Hyper-V
- Failover Clustering
- Microsoft 365 Integration

---

# About Me

I'm an aspiring Systems Administrator and IT Support professional with a strong interest in Windows infrastructure, Microsoft technologies, enterprise networking, and cybersecurity.

I built this home lab to gain practical, hands-on experience beyond theory and to document my learning in a way that reflects real-world IT administration.

---

⭐ If you found this project useful, feel free to star the repository.

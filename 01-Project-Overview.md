# Project Overview

## Table of Contents

- [Introduction](#introduction)
- [Project Purpose](#project-purpose)
- [Business Scenario](#business-scenario)
- [Project Objectives](#project-objectives)
- [Project Scope](#project-scope)
- [Technologies Used](#technologies-used)
- [Project Deliverables](#project-deliverables)
- [Documentation Structure](#documentation-structure)
- [Next Steps](#next-steps)

---

# Introduction

This repository documents my end-to-end Windows Server 2025 Enterprise Home Lab, which I built using VMware Workstation to simulate a real-world enterprise IT environment. Rather than configuring individual services in isolation, I designed the lab as a complete infrastructure project where each server role works together to provide centralized identity management, networking, security, storage, and backup services.

Throughout the project, I documented every stage of the deployment, from installing Windows Server to configuring Active Directory Domain Services (AD DS), DNS, DHCP, Group Policy, file services, storage, security, backup, and disaster recovery. Every configuration was validated through testing to ensure the environment functioned as expected.

This repository serves as both a technical reference and a portfolio project that demonstrates my practical Windows Server administration skills.

---

# Project Purpose

The purpose of this project was to move beyond theoretical learning by building and documenting a complete Windows Server environment from scratch.

By completing this project, I strengthened my understanding of how enterprise Windows infrastructures are designed, deployed, managed, secured, and maintained. I also focused on documenting every task in a structured and professional manner, reflecting the documentation standards commonly used by IT teams in production environments.

---

# Business Scenario

This home lab simulates the IT infrastructure of a small-to-medium-sized organization that requires centralized identity management, reliable network services, secure file sharing, endpoint protection, and data backup.

To meet these business requirements, I deployed Windows Server 2025 and configured multiple server roles that work together to provide a stable and manageable enterprise environment while remaining suitable for a home lab.

---

# Project Objectives

The primary objectives of this project were to:

- Deploy Windows Server 2025 in VMware Workstation.
- Configure Active Directory Domain Services (AD DS).
- Implement Domain Name System (DNS).
- Configure Dynamic Host Configuration Protocol (DHCP).
- Create and manage Organizational Units (OUs), users, groups, and computers.
- Configure Group Policy Objects (GPOs).
- Implement secure file sharing with NTFS permissions.
- Configure Storage Spaces for storage management.
- Enable Windows Defender and Windows Defender Firewall.
- Implement Windows Server Backup and recovery procedures.
- Validate every configuration through testing and troubleshooting.
- Produce professional documentation suitable for a technical portfolio.

---

# Project Scope

## Included

This repository documents the complete deployment and configuration of:

- Windows Server 2025
- VMware Workstation
- Active Directory Domain Services
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

## Not Included

The following technologies are outside the scope of this project:

- Microsoft Entra ID
- Microsoft Intune
- Microsoft Exchange Server
- Hyper-V
- Failover Clustering
- Microsoft Configuration Manager (SCCM)

These technologies are planned for future projects.

---

# Technologies Used

| Technology | Purpose |
|------------|---------|
| Windows Server 2025 | Server operating system |
| VMware Workstation | Virtualization platform |
| Active Directory Domain Services | Identity and access management |
| DNS | Name resolution |
| DHCP | Automatic IP address assignment |
| Group Policy | Centralized policy management |
| NTFS | File and folder permissions |
| Windows Defender | Endpoint protection |
| Windows Defender Firewall | Network security |
| Windows Server Backup | Backup and recovery |
| Event Viewer | System monitoring and troubleshooting |

---

# Project Deliverables

By the end of this project, I successfully deployed and documented:

- A fully configured Windows Server 2025 virtual machine.
- Active Directory Domain Services.
- DNS infrastructure.
- DHCP services.
- Organizational Units, users, and security groups.
- Group Policy Objects.
- Shared folders with NTFS permissions.
- Storage Spaces configuration.
- Windows Defender security configuration.
- Windows Defender Firewall configuration.
- Windows Server Backup implementation.
- Backup restoration and disaster recovery testing.
- Comprehensive technical documentation.

---

# Documentation Structure

The repository is organized into separate documentation files to make each topic easier to understand and navigate.

| Document | Description |
|----------|-------------|
| 01-Project-Overview.md | Overall introduction to the project |
| 02-Business-Scenario.md | Business requirements and project goals |
| 03-Lab-Environment.md | Hardware, software, and virtual environment |
| 04-Network-Architecture.md | Network design and topology |
| 05-VMware-Setup.md | Virtual machine creation and configuration |
| 06-Windows-Server-Installation.md | Windows Server installation |
| 07-Initial-Server-Configuration.md | Initial server configuration |
| 08-Active-Directory.md | Active Directory deployment |
| 09-DNS.md | DNS configuration |
| 10-DHCP.md | DHCP configuration |
| 11-Group-Policy.md | Group Policy configuration |
| 12-File-Server.md | File sharing and NTFS permissions |
| 13-Storage-Spaces.md | Storage management |
| 14-Windows-Defender.md | Endpoint protection |
| 15-Windows-Firewall.md | Firewall configuration |
| 16-Windows-Server-Backup.md | Backup implementation |
| 17-Event-Viewer.md | Monitoring and troubleshooting |
| 18-Disaster-Recovery.md | Backup restoration testing |
| 19-Troubleshooting.md | Issues encountered and resolutions |
| 20-Lessons-Learned.md | Key lessons from the project |
| 21-Skills-Demonstrated.md | Technical skills demonstrated |
| 22-Future-Improvements.md | Planned enhancements |

---

# Next Steps

The next document explains the business scenario that this Windows Server environment was designed to support.

➡️ Continue to **02-Business-Scenario.md**

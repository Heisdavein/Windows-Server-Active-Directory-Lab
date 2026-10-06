# Business Scenario

## Table of Contents

- [Overview](#overview)
- [Organization Background](#organization-background)
- [Business Challenges](#business-challenges)
- [Proposed Solution](#proposed-solution)
- [Project Goals](#project-goals)
- [Expected Business Outcomes](#expected-business-outcomes)
- [Server Roles Implemented](#server-roles-implemented)
- [Next Steps](#next-steps)

---

# Overview

This project simulates the deployment of a Windows Server 2025 infrastructure for a fictional small-to-medium-sized business. The goal is to create an enterprise-style environment where employees can securely access company resources, administrators can centrally manage users and devices, and essential network services operate reliably.

Although this environment was built as a home lab, I approached it as though I were deploying infrastructure for a real organization. Every configuration was planned, implemented, tested, and documented to reflect industry best practices.

---

# Organization Background

The fictional organization is experiencing steady growth and has reached a point where managing computers and user accounts individually is no longer practical.

As the company expands, it requires a centralized infrastructure that can:

- Authenticate users securely
- Manage employee accounts from a single location
- Automatically assign IP addresses
- Resolve hostnames across the network
- Enforce consistent security policies
- Provide secure file sharing
- Protect business data through backups
- Improve day-to-day IT administration

To meet these requirements, I designed and implemented a Windows Server 2025 environment using VMware Workstation.

---

# Business Challenges

Before implementing the new infrastructure, the organization faced several operational challenges.

## Decentralized User Management

Managing local user accounts on individual computers increases administrative overhead and makes it difficult to enforce consistent security policies.

---

## Manual Network Configuration

Without DHCP, every device would require a manually assigned IP address, increasing the likelihood of configuration errors and IP conflicts.

---

## Lack of Centralized Authentication

Without Active Directory, users would need separate local accounts on each computer, making password management and access control inefficient.

---

## Inconsistent Security Policies

Without Group Policy, configuring security settings individually on every device would consume valuable administrative time and increase the risk of inconsistent configurations.

---

## Limited File Management

The organization requires centralized file storage with controlled access based on departments and user permissions.

---

## Data Protection Risks

Without a structured backup strategy, accidental deletion, hardware failure, or system corruption could result in permanent data loss.

---

# Proposed Solution

To address these challenges, I deployed a Windows Server 2025 environment that provides centralized identity management, automated networking services, secure file storage, endpoint protection, and backup capabilities.

The infrastructure includes several Windows Server roles that work together to provide a stable, secure, and manageable environment suitable for a growing organization.

---

# Project Goals

The primary goals of this deployment were to:

- Deploy Windows Server 2025.
- Configure Active Directory Domain Services (AD DS).
- Implement DNS for reliable name resolution.
- Configure DHCP for automatic IP address assignment.
- Organize users and computers using Organizational Units (OUs).
- Apply centralized security policies through Group Policy.
- Configure secure shared folders with NTFS permissions.
- Implement Storage Spaces for storage management.
- Secure the environment using Windows Defender and Windows Defender Firewall.
- Protect business data through Windows Server Backup.
- Validate each service through testing and troubleshooting.
- Produce professional technical documentation for future reference.

---

# Expected Business Outcomes

After completing this deployment, the organization benefits from:

- Centralized user authentication
- Simplified user and computer management
- Automatic IP address allocation
- Reliable DNS name resolution
- Standardized security policies
- Secure departmental file sharing
- Improved infrastructure security
- Reliable backup and recovery procedures
- Easier system administration
- Better scalability for future growth

---

# Server Roles Implemented

| Server Role | Business Purpose |
|-------------|------------------|
| Active Directory Domain Services | Centralized identity and authentication |
| DNS Server | Resolves computer names to IP addresses |
| DHCP Server | Automatically assigns IP addresses to devices |
| Group Policy | Applies centralized configuration and security policies |
| File Server | Provides secure shared storage |
| Storage Spaces | Manages storage efficiently and improves flexibility |
| Windows Defender | Protects the server from malware and threats |
| Windows Defender Firewall | Controls inbound and outbound network traffic |
| Windows Server Backup | Protects business data through scheduled backups |
| Event Viewer | Assists with monitoring and troubleshooting |

---

# Next Steps

With the business requirements clearly defined, the next step is to examine the hardware, software, and virtual infrastructure used to build the lab environment.

➡️ Continue to **03-Lab-Environment.md**

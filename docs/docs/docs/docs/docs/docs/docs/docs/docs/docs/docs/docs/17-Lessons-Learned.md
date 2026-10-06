# 17 Lessons Learned

## Table of Contents

- [Overview](#overview)
- [1. Practical Experience Is Irreplaceable](#1-practical-experience-is-irreplaceable)
- [2. Windows Server Services Are Connected](#2-windows-server-services-are-connected)
- [3. DNS Is Critical to Active Directory](#3-dns-is-critical-to-active-directory)
- [4. Configuration Should Be Validated](#4-configuration-should-be-validated)
- [5. Group Policy Makes Centralised Management Possible](#5-group-policy-makes-centralised-management-possible)
- [6. Organisation Matters as an Environment Grows](#6-organisation-matters-as-an-environment-grows)
- [7. Security Requires Multiple Layers](#7-security-requires-multiple-layers)
- [8. Troubleshooting Builds Real Understanding](#8-troubleshooting-builds-real-understanding)
- [9. Documentation Matters](#9-documentation-matters)
- [10. The Lab Changed How I Learn](#10-the-lab-changed-how-i-learn)
- [Final Reflection](#final-reflection)

---

## Overview

When I started this home lab, I understood many Windows Server concepts from a theoretical point of view, but I had very little hands-on experience putting those concepts into practice.

Building the environment myself changed that.

Instead of only reading about Active Directory, DNS, DHCP, Group Policy, file servers, storage, firewalls, antivirus protection, backups, and Event Viewer, I configured these technologies and tested how they worked together.

The biggest lesson I took from the project is simple:

> **Practical experience is irreplaceable.**

Reading documentation and watching tutorials helped me understand the concepts, but building the environment myself, troubleshooting problems, and fixing configuration issues gave me a level of confidence that theory alone could not provide.

---

## 1. Practical Experience Is Irreplaceable

One of the main reasons I built this lab was to move beyond theory.

Before starting the project, I understood many Windows Server concepts, but understanding a technology and actually administering it are two different things.

Working through the lab gave me the opportunity to:

- Build a Windows Server 2025 virtual machine.
- Configure a Domain Controller.
- Create and manage an Active Directory domain.
- Configure DNS.
- Configure DHCP.
- Create users, groups, and Organisational Units.
- Join a second server to the domain.
- Create and apply Group Policy.
- Configure Storage Spaces.
- Build a file server.
- Configure NTFS permissions and security group-based access.
- Configure Windows Defender Firewall.
- Verify Microsoft Defender Antivirus.
- Work with Windows Server Backup.
- Use Event Viewer for monitoring and troubleshooting.

These were not just concepts I read about. I actually worked through them inside my own environment.

That difference made the knowledge much more meaningful.

---

## 2. Windows Server Services Are Connected

Another important lesson was that Windows Server is not simply a collection of unrelated features.

The services depend on and interact with each other.

For example:

- Active Directory depends heavily on DNS.
- DHCP provides network settings that point clients toward the Domain Controller.
- Group Policy relies on the organisational structure created in Active Directory.
- NTFS permissions work alongside security groups to control access to shared resources.
- File sharing depends on the underlying network and permissions being configured correctly.

Seeing these relationships in practice helped me understand Windows Server as an integrated environment rather than a collection of individual tools.

This was one of the biggest differences between simply studying the technologies and actually building them.

---

## 3. DNS Is Critical to Active Directory

Working with the second server reinforced just how important DNS is in an Active Directory environment.

When I prepared `server02` to join the domain, I configured its preferred DNS server as:

```text
192.168.1.222
```

This was the IP address of `server01`, my Domain Controller and DNS server.

Before joining the domain, I tested DNS connectivity with:

```cmd
ping server01.corp.danieltraining.com
```

The hostname successfully resolved to the IP address of `server01` and returned replies.

This confirmed that `server02` could locate the Domain Controller through DNS before I attempted the domain join.

The project made the relationship much clearer to me: DNS is not just something used to translate names into IP addresses. In an Active Directory environment, it is a fundamental part of how computers locate domain services.

---

## 4. Configuration Should Be Validated

Another lesson I learned was not to assume that a configuration worked simply because I had completed the configuration steps.

I repeatedly used testing and verification throughout the lab.

For example, I used:

```cmd
ipconfig /release
ipconfig /renew
ipconfig /all
```

to verify DHCP configuration.

I used:

```cmd
ping router
```

and:

```cmd
nslookup 192.168.1.1
```

to test DNS resolution.

I used:

```cmd
gpupdate /force
```

when testing Group Policy changes.

I used:

```cmd
netdom query fsmo
```

to identify the FSMO role holders.

I also verified the health of Storage Spaces using PowerShell commands such as:

```powershell
Get-StoragePool
```

```powershell
Get-VirtualDisk
```

and:

```powershell
Get-Volume
```

The pattern became clear:

**Configure → Test → Verify → Continue.**

That approach gave me much more confidence in the environment than simply assuming each configuration was correct.

---

## 5. Group Policy Makes Centralised Management Possible

Group Policy was one of the technologies that helped me understand the value of centralised administration.

Instead of configuring every computer individually, I could create a policy once and link it to the appropriate Organisational Unit.

For example, I created:

```text
Disable Control Panel - End Users
```

and linked it to the `End Users` OU.

The policy controlled:

```text
User Configuration
→ Policies
→ Administrative Templates
→ Control Panel
→ Prohibit access to Control Panel and PC Settings
```

I then used:

```cmd
gpupdate /force
```

to immediately apply the updated policy while testing.

The project also introduced me to the **LSDOU** processing order:

```text
Local
Site
Domain
Organisational Unit
```

Understanding this helped me see how policies can be applied at different levels and why organisation within Active Directory matters. 

---

## 6. Organisation Matters as an Environment Grows

Creating the `Daniel Training` structure inside Active Directory also showed me why good organisation matters.

My structure included:

```text
corp.danieltraining.com
└── Daniel Training
    ├── End Users
    ├── Security Groups
    └── Servers
```

I also moved `server02` from the default `Computers` container into the `Servers` OU.

This was not simply about making the directory look tidy.

Organising objects into appropriate OUs makes it easier to manage systems and apply policies as an environment grows.

The same principle appeared elsewhere in the project.

For example, I used security groups to help manage access to shared resources, and I organised storage and file-sharing components into clearly defined structures.

The lab showed me that administration is not only about making something work. It is also about making the environment manageable.

---

## 7. Security Requires Multiple Layers

The security sections of the project taught me that protecting a server is not a single-task process.

Microsoft Defender Antivirus provided malware protection.

Windows Defender Firewall controlled network traffic.

Active Directory controlled identities and access.

Group Policy allowed me to centrally apply security-related settings.

NTFS permissions and security groups controlled access to shared resources.

Windows Server Backup provided a recovery capability.

Event Viewer provided visibility into system and security events.

The Defender section especially reinforced this idea. I learned that effective server security comes from combining multiple layers, including regular Windows updates, a properly configured firewall, strong access controls, and continuously updated antivirus protection.

I also learned an important firewall principle while creating the custom TCP 8080 rule:

> **Only open the ports that are absolutely necessary.**

This follows the **Principle of Least Privilege**, where systems should receive only the access they actually require.

---

## 8. Troubleshooting Builds Real Understanding

One of the most valuable parts of the lab was having to verify configurations and work through problems instead of simply reading about them.

Troubleshooting forced me to ask questions such as:

- Is the server reachable?
- Is DNS resolving the correct name?
- Did DHCP provide the expected configuration?
- Did Group Policy actually apply?
- Can the client locate the Domain Controller?
- Are the required firewall rules present?
- Are storage resources healthy?
- Can users access the correct shared resources?
- What do the Windows event logs show?

This changed the way I think about technical problems.

Instead of immediately assuming that something is broken, I learned to check each part of the environment and use evidence to narrow down the cause.

That mindset is useful far beyond this particular Windows Server lab.

---

## 9. Documentation Matters

Another lesson I gained from the project was the importance of documenting infrastructure clearly.

Throughout the lab, I recorded:

- IP addresses.
- Server names.
- Domain information.
- DNS records.
- DHCP settings.
- Active Directory structure.
- Group Policy configuration.
- Storage configuration.
- File server configuration.
- Firewall rules.
- Security settings.
- Verification commands.
- Recovery information.

This became especially important when working with Active Directory and FSMO roles.

I learned that during an outage or disaster recovery situation is the worst possible time to discover that critical infrastructure information has not been documented. 

Good documentation makes troubleshooting, maintenance, recovery, and future changes easier.

---

## 10. The Lab Changed How I Learn

Perhaps the biggest change was in how I approach learning technical subjects.

At the beginning, I could understand a concept from a tutorial or piece of documentation.

After building the lab, I started asking different questions:

**Why does this service exist?**

**What does it depend on?**

**What happens if I change this setting?**

**How can I verify that it is working?**

**How would I troubleshoot it if it stopped working?**

That shift from simply following instructions to understanding what I am actually doing became one of the most valuable parts of the project.

The original project was deliberately built around understanding why each technology exists, how it works, and how it connects with the rest of the environment.

---

## Final Reflection

When I started this project, I had a fresh Windows Server 2025 installation, VMware Workstation, and a willingness to learn.

By the end, I had built a functioning Windows Server environment containing:

- Active Directory Domain Services.
- DNS.
- DHCP.
- Organisational Units.
- User accounts.
- Security groups.
- A second domain-joined server.
- Group Policy.
- Storage Spaces.
- A file server.
- NTFS permissions.
- Security group-based access control.
- Windows Defender Firewall.
- Microsoft Defender Antivirus.
- Windows Server Backup.
- Event Viewer.

More importantly, I gained a better understanding of how these technologies work together.

The project reinforced something I already suspected but now understand through experience:

> **You can read about technology for a long time, but building it yourself teaches you differently.**

The hands-on work, validation, troubleshooting, and configuration decisions gave me confidence that I could not have gained from theory alone.

This lab has therefore become more than a collection of Windows Server exercises. It is practical experience that I can continue building on as I develop my skills in systems administration, IT support, infrastructure, and cybersecurity.

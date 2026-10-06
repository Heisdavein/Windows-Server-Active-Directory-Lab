# 18- Skills Demonstrated

## Table of Contents

- [Overview](#overview)
- [1. Virtualisation and Lab Infrastructure](#1-virtualisation-and-lab-infrastructure)
- [2. Windows Server Administration](#2-windows-server-administration)
- [3. Active Directory Domain Services](#3-active-directory-domain-services)
- [4. DNS Administration](#4-dns-administration)
- [5. DHCP Administration](#5-dhcp-administration)
- [6. Group Policy](#6-group-policy)
- [7. Windows File Server Administration](#7-windows-file-server-administration)
- [8. Storage Spaces](#8-storage-spaces)
- [9. Windows Firewall](#9-windows-firewall)
- [10. Microsoft Defender Antivirus](#10-microsoft-defender-antivirus)
- [11. Backup and Recovery](#11-backup-and-recovery)
- [12. Event Viewer and System Monitoring](#12-event-viewer-and-system-monitoring)
- [13. Network and Connectivity Troubleshooting](#13-network-and-connectivity-troubleshooting)
- [14. Validation and Testing](#14-validation-and-testing)
- [15. Technical Documentation](#15-technical-documentation)
- [Skills Summary](#skills-summary)

---

# Overview

This project gave me practical experience with many of the core technologies used in Windows-based IT infrastructure.

I did not only study these technologies theoretically. I built and configured them inside my own VMware-based Windows Server 2025 environment, tested their operation, and documented the results.

The project specifically gave me experience with:

- VMware virtualisation
- Windows Server 2025
- Active Directory Domain Services
- DNS
- DHCP
- Group Policy
- Windows file services
- NTFS permissions
- Security groups
- Storage Spaces
- Windows Defender Firewall
- Microsoft Defender Antivirus
- Windows Server Backup
- Event Viewer
- Network troubleshooting
- System validation
- Technical documentation

The original project describes these as practical skills relevant to Windows Server administration, IT support, and system administration work.

---

# 1. Virtualisation and Lab Infrastructure

I used **VMware Workstation** to build the Windows Server environment on my Windows 11 computer.

Using virtualisation allowed me to create a separate server environment without installing Windows Server directly onto my main operating system.

### Skills demonstrated

- Creating virtual machines.
- Installing Windows Server 2025 inside a virtual machine.
- Allocating virtual CPU, RAM, storage, and networking resources.
- Attaching Windows Server installation media.
- Configuring virtual network connectivity.
- Using VMware snapshots for experimentation and recovery.
- Building a second Windows Server virtual machine.

My lab environment allowed me to safely experiment, test configurations, troubleshoot problems, and rebuild or restore systems without directly affecting my main Windows 11 installation.

---

# 2. Windows Server Administration

I built my environment around **Windows Server 2025 Evaluation**.

The project covered the initial configuration of Windows Server and progressively expanded the server into a multi-role infrastructure platform.

### Skills demonstrated

- Installing Windows Server 2025.
- Configuring server networking.
- Assigning static IP addresses.
- Configuring server names.
- Enabling and configuring Remote Desktop.
- Managing Server Manager.
- Installing Windows Server roles.
- Managing server roles and features.
- Working with Windows Server administration tools.
- Managing multiple Windows Server systems.

The final environment included `server01` and `server02`, allowing me to work with more than one Windows Server inside the same domain.

---

# 3. Active Directory Domain Services

One of the main components of the project was building an **Active Directory Domain Services (AD DS)** environment.

I configured `server01` as the Domain Controller for:

```text
corp.danieltraining.com
```

I then created a structured Active Directory environment containing users, computers, Organisational Units, and security groups.

### Skills demonstrated

- Installing and configuring AD DS.
- Promoting a Windows Server to a Domain Controller.
- Creating an Active Directory domain.
- Creating Organisational Units.
- Creating user accounts.
- Creating security groups.
- Managing computer accounts.
- Joining another Windows Server to the domain.
- Organising domain objects.
- Checking FSMO role ownership.

I also used:

```cmd
netdom query fsmo
```

to identify the five FSMO role holders.

Because my lab contained one Domain Controller, all five FSMO roles were held by `server01`. 

---

# 4. DNS Administration

I configured DNS on `server01` as part of the Active Directory environment.

The project included both forward and reverse DNS functionality.

### Skills demonstrated

- Configuring DNS Server.
- Creating forward lookup records.
- Creating A records.
- Creating CNAME records.
- Creating reverse lookup zones.
- Creating PTR records.
- Testing forward DNS resolution.
- Testing reverse DNS resolution.
- Understanding DNS's relationship with Active Directory.

The project included records such as:

```text
router → 192.168.1.1
gateway → CNAME to router
```

I also tested reverse resolution using:

```cmd
nslookup 192.168.1.1
```

and expected the result to resolve to:

```text
router.corp.danieltraining.com
```

Working with `server02` also demonstrated the importance of using the Domain Controller as the preferred DNS server when joining a computer to the Active Directory domain.

---

# 5. DHCP Administration

I installed and configured the **DHCP Server** role on `server01`.

The DHCP service was configured to automatically provide network settings to clients instead of requiring every device to be configured manually.

### Skills demonstrated

- Installing the DHCP Server role.
- Creating a DHCP scope.
- Defining an IP address range.
- Configuring the subnet mask.
- Configuring the default gateway.
- Configuring the DNS server.
- Configuring lease duration.
- Activating the DHCP scope.
- Checking DHCP address leases.
- Testing DHCP address assignment.

The configured scope was:

```text
Scope Name: LAN Network – Lab Environment
Start:      192.168.1.5
End:        192.168.1.99
Subnet:     255.255.255.0
Gateway:    192.168.1.1
DNS:        192.168.1.222
Lease:      8 days
```

I validated the configuration using:

```cmd
ipconfig /release
ipconfig /renew
ipconfig /all
```

This allowed me to confirm that the client received an address from the configured DHCP range and the expected gateway and DNS server.

---

# 6. Group Policy

I gained hands-on experience with **Group Policy**, which allows administrators to centrally control settings instead of configuring individual computers manually.

I created the following GPO:

```text
Disable Control Panel - End Users
```

The policy was linked to the `End Users` OU.

The configured policy was:

```text
User Configuration
→ Policies
→ Administrative Templates
→ Control Panel
→ Prohibit access to Control Panel and PC Settings
```

### Skills demonstrated

- Opening Group Policy Management.
- Creating a custom GPO.
- Linking a GPO to an OU.
- Understanding Group Policy processing.
- Understanding LSDOU.
- Reviewing default domain security settings.
- Applying policy changes.
- Testing Group Policy.
- Using `gpupdate /force`.

I also learned how Group Policy can scale administrative tasks. Instead of configuring hundreds of computers individually, a policy can be created once and applied to the appropriate users or computers through Active Directory. 

---

# 7. Windows File Server Administration

I configured `server01` to provide file-sharing services.

The main project share was:

```text
Z:\Projects
```

and the SMB share was:

```text
\\server01\Projects
```

The share description was:

```text
Projects folder – Staff access
```

### Skills demonstrated

- Configuring Windows file services.
- Creating shared folders.
- Configuring SMB shares.
- Working with NTFS permissions.
- Using security groups for access control.
- Configuring mapped network drives.
- Testing file-share access.
- Using Access-Based Enumeration.
- Connecting to shared resources from another domain-joined server.

I also mapped the network share as a network drive with **Reconnect at sign-in** enabled.

This gave me practical experience with the relationship between shared folders, NTFS permissions, security groups, and user access.

---

# 8. Storage Spaces

I configured **Windows Storage Spaces** using five additional virtual hard disks.

Each disk was:

```text
10 GB
```

The five disks were combined into:

```text
Lab Storage Pool
```

I then created:

```text
Lab Virtual Disk
```

using the **Parity** storage layout.

The virtual disk was presented as:

```text
Z:
```

with the volume label:

```text
Storage Pool
```

### Skills demonstrated

- Adding virtual disks to a Windows Server.
- Bringing disks online.
- Initialising disks using GPT.
- Creating Storage Pools.
- Creating virtual disks.
- Configuring parity storage.
- Using thin provisioning.
- Creating and formatting volumes.
- Assigning drive letters.
- Verifying storage health with PowerShell.

I used:

```powershell
Get-StoragePool
Get-VirtualDisk
Get-Volume
```

to verify the resulting storage configuration.

This gave me practical experience with resilient storage using Windows Storage Spaces.

---

# 9. Windows Firewall

I worked with **Windows Defender Firewall with Advanced Security** to understand how Windows Server controls network traffic.

I reviewed the difference between inbound and outbound rules and examined rules automatically created for installed server roles.

I also created a custom inbound firewall rule.

### Custom rule

```text
Rule Name: Allow TCP 8080 – Web App
Protocol: TCP
Port: 8080
Action: Allow
Profiles: Domain, Private, Public
```

### Skills demonstrated

- Opening Windows Defender Firewall with Advanced Security.
- Understanding inbound firewall rules.
- Understanding outbound firewall rules.
- Reviewing built-in server-role firewall rules.
- Creating custom inbound rules.
- Allowing specific TCP ports.
- Applying firewall rules to network profiles.
- Understanding the Principle of Least Privilege.

The project reinforced the importance of opening only the ports that are actually required.

---

# 10. Microsoft Defender Antivirus

I also configured and verified **Microsoft Defender Antivirus** on Windows Server 2025.

### Skills demonstrated

- Verifying Microsoft Defender status.
- Checking Windows Security.
- Running Quick Scan.
- Reviewing Full Scan options.
- Updating security intelligence.
- Reviewing Defender protection settings.
- Understanding layered server security.

I verified that **Virus & threat protection** showed healthy protection and also confirmed that **App & browser control** was enabled.

I then performed a Quick Scan and explored the Full Scan option.

I also manually checked for security intelligence updates.

This gave me practical experience with the built-in antivirus capabilities of Windows Server 2025.

---

# 11. Backup and Recovery

The project included **Windows Server Backup** as part of the server's data-protection strategy.

The documented implementation included:

- Scheduled backups.
- Recovery testing.
- A successful file recovery exercise.

This gave me practical exposure to the idea that backup configuration should not stop at creating backups. Recovery also needs to be tested.

### Skills demonstrated

- Working with Windows Server Backup.
- Understanding scheduled server backups.
- Understanding recovery workflows.
- Testing file recovery.

The original project does not document the exact backup schedule, destination, retention settings, or recovery commands, so I have not added any undocumented configuration here.

---

# 12. Event Viewer and System Monitoring

I used **Event Viewer** to monitor and analyse Windows system and security events.

I worked with important security event IDs including:

| Event ID | Meaning |
|---|---|
| `4624` | Successful user logon |
| `4625` | Failed logon attempt |
| `4740` | User account locked out |
| `4720` | User account created |
| `4726` | User account deleted |
| `4728` | User added to a security-enabled global group |

I also created a custom Event Viewer view called:

```text
All Errors and Criticals
```

This view was configured to display critical and error events across Windows logs.

### Skills demonstrated

- Navigating Event Viewer.
- Reviewing Windows security events.
- Understanding important security event IDs.
- Creating custom Event Viewer views.
- Filtering events.
- Using event information for troubleshooting and analysis.

---

# 13. Network and Connectivity Troubleshooting

The project gave me practical experience troubleshooting network connectivity and name resolution.

Examples included:

```cmd
ping server01.corp.danieltraining.com
```

```cmd
ping router
```

```cmd
nslookup 192.168.1.1
```

```cmd
ipconfig /release
ipconfig /renew
ipconfig /all
```

These commands helped me verify different parts of the environment.

For example, I used DNS resolution testing before joining `server02` to the Active Directory domain.

This helped me understand that troubleshooting should begin by identifying which part of the communication path is actually failing rather than assuming the problem is with the application or server itself.

---

# 14. Validation and Testing

Testing was built into different stages of the project.

I did not treat configuration as complete simply because the wizard finished successfully.

I used verification methods such as:

- DNS resolution tests.
- Reverse DNS lookups.
- DHCP lease verification.
- Group Policy refresh.
- FSMO role verification.
- Storage health checks.
- File-share access testing.
- Firewall rule review.
- Defender status checks.
- Antivirus scanning.
- Event Viewer filtering.
- Backup recovery testing.

This demonstrates an important administration skill:

> **Configure it, then prove that it works.**

The project specifically describes validation, troubleshooting, and testing as part of the practical experience gained from the lab.

---

# 15. Technical Documentation

A major part of this project was documenting what I built and why I built it.

The original documentation was written from my own perspective and explained not only the configuration steps but also the purpose behind the technologies and how they fit into the wider Windows Server environment.

### Skills demonstrated

- Technical writing.
- Configuration documentation.
- Recording infrastructure values.
- Documenting verification procedures.
- Explaining technical concepts clearly.
- Organising a complex infrastructure project.
- Recording troubleshooting and validation methods.

This repository continues that approach by separating the project into focused documentation files rather than placing the entire project into one large document.

---

# Skills Summary

| Skill Area | Hands-On Experience |
|---|---|
| Virtualisation | VMware Workstation, virtual machines, snapshots |
| Windows Server | Windows Server 2025 administration |
| Active Directory | AD DS, Domain Controller, users, groups, OUs, computers |
| DNS | A, CNAME, PTR records, forward/reverse lookup |
| DHCP | Scope creation, IP assignment, leases, network options |
| Group Policy | GPO creation, OU linking, policy refresh, LSDOU |
| File Services | SMB shares, NTFS permissions, mapped drives |
| Storage | Storage Spaces, storage pools, parity virtual disk |
| Firewall | Built-in rules and custom TCP 8080 rule |
| Endpoint Security | Microsoft Defender Antivirus |
| Backup | Windows Server Backup and recovery testing |
| Monitoring | Event Viewer and custom views |
| Troubleshooting | Network, DNS, DHCP, Group Policy, storage and system validation |
| Documentation | Technical procedures, configuration records, verification |

---

## Overall Capability

This project gave me practical exposure to the core technologies that make up a Windows-based enterprise environment.

More importantly, I learned how those technologies interact.

I configured individual services, but I also learned to look at the environment as a complete system:

**Virtualisation → Windows Server → Active Directory → DNS → DHCP → Group Policy → File Services → Storage → Security → Backup → Monitoring → Troubleshooting**

That experience is the main value of this project.

I did not simply collect configuration steps. I built the environment, tested it, documented it, and developed a better understanding of how the individual components work together.

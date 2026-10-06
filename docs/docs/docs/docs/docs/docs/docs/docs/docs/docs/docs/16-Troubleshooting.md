# 16 — Troubleshooting

## Table of Contents

- [Overview](#overview)
- [My Troubleshooting Approach](#my-troubleshooting-approach)
- [Network and Connectivity Troubleshooting](#network-and-connectivity-troubleshooting)
- [DNS Troubleshooting](#dns-troubleshooting)
- [Active Directory and Domain Join Troubleshooting](#active-directory-and-domain-join-troubleshooting)
- [DHCP Troubleshooting](#dhcp-troubleshooting)
- [Group Policy Troubleshooting](#group-policy-troubleshooting)
- [FSMO Role Verification](#fsmo-role-verification)
- [File Server Troubleshooting](#file-server-troubleshooting)
- [Windows Firewall Troubleshooting](#windows-firewall-troubleshooting)
- [Event Viewer for Troubleshooting](#event-viewer-for-troubleshooting)
- [Recovery When Configuration Goes Wrong](#recovery-when-configuration-goes-wrong)
- [Troubleshooting Workflow](#troubleshooting-workflow)
- [Verification Checklist](#verification-checklist)
- [Screenshots](#screenshots)
- [Skills Demonstrated](#skills-demonstrated)
- [Key Takeaway](#key-takeaway)

---

## Overview

Troubleshooting was one of the most important parts of building this Windows Server 2025 environment.

A server can have all the correct roles installed and still fail to provide the expected service if one configuration is incorrect.

During this project, I learned that troubleshooting should not start with randomly changing settings.

Instead, I learned to work through the environment logically:

1. Identify what is not working.
2. Check the underlying configuration.
3. Test connectivity or functionality.
4. Use the appropriate administrative tool or command.
5. Review the results.
6. Make the required change.
7. Test again.

This approach helped me understand how the different Windows Server services depend on one another.

---

# My Troubleshooting Approach

I approached troubleshooting by breaking a problem down into smaller parts.

For example, if a server could not join the Active Directory domain, I would not immediately assume that Active Directory itself was broken.

I would first check whether the server could communicate with the Domain Controller and whether DNS was resolving the Domain Controller's hostname correctly.

Likewise, if a DHCP client did not appear to have the correct network configuration, I could force it to release and renew its DHCP lease and then inspect the complete network configuration.

This gave me a more structured way of finding problems.

> **Key principle:**  
> I learned to verify the basics first instead of immediately changing advanced configurations.

---

# Network and Connectivity Troubleshooting

Network connectivity was important throughout the project because almost every major service depended on communication between systems.

My environment used:

- `server01`
- `server02`
- `192.168.1.222` for server01
- `192.168.1.223` for server02
- `192.168.1.1` for the router
- `255.255.255.0` as the subnet mask

I used basic connectivity and name-resolution testing before moving on to more advanced configuration.

### Checking the Domain Controller by Hostname

From server02, I tested communication with server01 using:

```cmd
ping server01.corp.danieltraining.com
```

The hostname successfully resolved to the IP address of server01 and returned reply packets.

This confirmed two important things:

- server02 could communicate with server01.
- DNS could resolve the Domain Controller's hostname.

This was an important prerequisite before attempting the domain join.

---

# DNS Troubleshooting

DNS became one of the most important troubleshooting areas in the project because Active Directory depends heavily on DNS.

When preparing server02 to join the domain, I configured its preferred DNS server as:

```text
192.168.1.222
```

This was the IP address of server01, which was running the DNS Server role.

## Why the DNS Configuration Matters

A common mistake would be to configure a domain-joined computer to use a public DNS server such as:

```text
1.1.1.1
```

or:

```text
8.8.8.8
```

The project documentation explains that this would prevent the computer from locating the Domain Controller correctly during the domain-join process.

For my Active Directory environment, the preferred DNS server needed to point to the Domain Controller.

### DNS Connectivity Test

Before joining server02 to the domain, I ran:

```cmd
ping server01.corp.danieltraining.com
```

The successful response confirmed that DNS was working well enough for server02 to locate server01.

### DNS Record Testing

I also configured and tested DNS records on server01.

For example:

```text
router → 192.168.1.1
```

and:

```text
gateway → CNAME → router
```

I tested forward resolution using:

```cmd
ping router
```

I also tested reverse DNS resolution using:

```cmd
nslookup 192.168.1.1
```

The expected reverse lookup result was:

```text
router.corp.danieltraining.com
```

These tests helped confirm that both forward and reverse DNS resolution were functioning as expected.

---

# Active Directory and Domain Join Troubleshooting

Joining server02 to the domain provided an excellent example of why troubleshooting should be performed in stages.

Before attempting the domain join, I checked the server's network configuration.

The relevant settings were:

| Setting | server02 |
|---|---|
| Computer Name | `server02` |
| Static IP | `192.168.1.223` |
| Preferred DNS | `192.168.1.222` |
| Domain | `corp.danieltraining.com` |

I then tested:

```cmd
ping server01.corp.danieltraining.com
```

Only after confirming successful communication and name resolution did I proceed with the domain join.

### Domain Join Verification

After joining server02 to the domain, Windows displayed:

```text
Welcome to the corp.danieltraining.com domain.
```

I then restarted server02.

From server01, I opened **Active Directory Users and Computers** and verified that server02 had been added to the domain.

I also moved the computer object into the:

```text
Servers
```

OU.

This gave me multiple points of verification rather than relying only on the successful domain-join message.

---

# DHCP Troubleshooting

After configuring DHCP, I wanted to verify that clients were actually receiving the expected network settings.

Rather than waiting for the existing lease to expire, I forced the client to request a new lease.

I used:

```cmd
ipconfig /release
ipconfig /renew
ipconfig /all
```

Each command served a different purpose.

### `ipconfig /release`

This released the client's current DHCP-assigned IP address.

### `ipconfig /renew`

This requested a new DHCP lease.

### `ipconfig /all`

This displayed the complete network configuration so I could verify the result.

### What I Verified

I checked that the client received:

- An IP address within `192.168.1.5 – 192.168.1.99`
- Default Gateway: `192.168.1.1`
- DNS Server: `192.168.1.222`

This confirmed that DHCP was supplying the expected network configuration.

### DHCP Manager

I also learned how to inspect DHCP activity through DHCP Manager.

The areas I could review included:

- **Address Pool**
- **Address Leases**
- **Reservations**

This provided another way to verify what addresses the DHCP server was managing and leasing.

---

# Group Policy Troubleshooting

Group Policy changes do not always need to wait for the normal policy refresh cycle when I am testing a configuration.

For my custom policy, I used:

```cmd
gpupdate /force
```

This forces Windows to immediately download and apply the latest Group Policy settings.

This was particularly useful while testing my custom GPO:

```text
Disable Control Panel - End Users
```

The policy was linked to:

```text
End Users
```

and configured under:

```text
User Configuration
→ Policies
→ Administrative Templates
→ Control Panel
```

The policy itself was:

```text
Prohibit access to Control Panel and PC Settings
```

I set the policy to **Enabled**.

Using `gpupdate /force` allowed me to apply the latest policy configuration without waiting for the normal refresh interval.

---

# FSMO Role Verification

Another useful troubleshooting and administration check involved identifying the FSMO role holders in Active Directory.

I used:

```cmd
netdom query fsmo
```

Because my lab contained only one Domain Controller, all five FSMO roles were held by:

```text
server01
```

Knowing this information is important when investigating Active Directory problems because the FSMO roles perform specialised tasks within the domain.

The command also gave me a quick way to verify which Domain Controller was responsible for these roles.

---

# File Server Troubleshooting

The file server was built on server01 using the `Z:` drive created through Storage Spaces.

The project share was configured as:

```text
Z:\Projects
```

The SMB share was:

```text
Projects
```

and the network path was:

```text
\\server01\Projects
```

The share description was:

```text
Projects folder – Staff access
```

I enabled **Access-Based Enumeration** on the share.

The project also included:

- NTFS permissions
- Security group-based access control
- Mapped network drives
- Testing access from server02

### File Access Troubleshooting Approach

If access to the share did not behave as expected, the relevant areas to check would be the configuration already established in the project:

1. Confirm that the `Z:` volume is available.
2. Confirm that `Z:\Projects` exists.
3. Confirm that the SMB share is online.
4. Confirm the network path:
   ```text
   \\server01\Projects
   ```
5. Check NTFS permissions.
6. Check security group membership.
7. Verify that the client can communicate with server01.

The important lesson here was that file access can depend on multiple layers rather than one setting.

---

# Windows Firewall Troubleshooting

Windows Firewall was another area where understanding the relationship between services and network ports became important.

Windows Server automatically creates firewall rules for many installed roles.

Examples documented in the project include:

| Service | Protocol / Port |
|---|---|
| DHCP | UDP 67 |
| DHCP | UDP 68 |
| DNS | TCP 53 |
| DNS | UDP 53 |
| Active Directory Domain Services | Multiple rules for authentication, directory services, replication, and related functions |

I also created a custom inbound firewall rule.

### Custom Rule

```text
Rule Name: Allow TCP 8080 – Web App
Protocol: TCP
Local Port: 8080
Action: Allow the connection
Profiles: Domain, Private, Public
```

The rule was created to allow incoming traffic on TCP port `8080`.

### Troubleshooting Principle

If a service is expected to receive network traffic but connections are failing, the firewall should be one of the areas investigated.

However, I also learned that firewall troubleshooting should not simply mean opening every port.

The project reinforced the **Principle of Least Privilege**:

> Only open the ports that are actually required.

---

# Event Viewer for Troubleshooting

Event Viewer became one of my main troubleshooting tools.

Instead of relying only on visible symptoms, I could investigate the events Windows had recorded.

The main Windows logs I worked with included:

- Application
- Security
- Setup
- System
- Forwarded Events

I also learned how to filter logs by event severity.

For example, I could select:

- Critical
- Error

I could also filter by Event ID.

Examples from the Security log included:

| Event ID | Meaning |
|---|---|
| `4624` | Successful user logon |
| `4625` | Failed logon attempt |
| `4740` | User account locked out |
| `4720` | User account created |
| `4726` | User account deleted |
| `4728` | User added to a security-enabled global group |

### Custom Troubleshooting View

I created a custom Event Viewer view called:

```text
All Errors and Criticals
```

The view was configured to display:

- Critical events
- Error events
- All Windows Logs

This gave me a faster way to identify serious problems without manually checking every log.

---

# Recovery When Configuration Goes Wrong

Because I was building the environment as a learning lab, I also planned for the possibility that a configuration change could cause problems.

Before installing Windows Server, I created a VMware snapshot named:

```text
Before Install
```

This provided a clean recovery point.

If the installation failed or I wanted to restart from the original state, I could return to that snapshot instead of creating the entire virtual machine again.

The project also included Windows Server Backup with scheduled backups and recovery testing, including a successful file recovery exercise.

This reinforced an important troubleshooting lesson:

> Troubleshooting is not only about fixing problems. It is also about having a safe way to recover when an experiment does not produce the expected result.

---

# Troubleshooting Workflow

The troubleshooting process I developed through the project can be represented as:

```text
Identify the Problem
        ↓
Check Basic Configuration
        ↓
Check Network Connectivity
        ↓
Check DNS / Name Resolution
        ↓
Check the Relevant Service
        ↓
Run a Targeted Test
        ↓
Review Event Logs
        ↓
Make the Required Change
        ↓
Test Again
        ↓
Confirm the Expected Result
```

I found this approach much more useful than making multiple changes at once.

If several settings are changed simultaneously, it becomes difficult to know which change actually solved the problem.

---

# Verification Checklist

The following checks were used throughout the project to validate different parts of the environment.

| Area | Verification |
|---|---|
| Server networking | Verify static IP configuration |
| DNS | `ping server01.corp.danieltraining.com` |
| Reverse DNS | `nslookup 192.168.1.1` |
| DHCP | `ipconfig /release` |
| DHCP | `ipconfig /renew` |
| DHCP | `ipconfig /all` |
| Group Policy | `gpupdate /force` |
| FSMO | `netdom query fsmo` |
| Storage | `Get-StoragePool` |
| Storage | `Get-VirtualDisk` |
| Storage | `Get-Volume` |
| Event investigation | Filter by Event ID and severity |
| File Server | Test `\\server01\Projects` |
| Recovery | Perform file recovery testing |

These checks helped me validate individual services instead of assuming that an installation automatically meant the service was working correctly.

---

# Screenshots

> 📸 **Screenshot Placeholder**  
> **Filename:** `16-01-dns-connectivity-test.png`  
> **Description:** Command Prompt on server02 showing `ping server01.corp.danieltraining.com` and the successful replies used to verify DNS connectivity to server01.

> 📸 **Screenshot Placeholder**  
> **Filename:** `16-02-dhcp-ipconfig-test.png`  
> **Description:** Command Prompt showing `ipconfig /release`, `ipconfig /renew`, and `ipconfig /all` used to verify DHCP configuration.

> 📸 **Screenshot Placeholder**  
> **Filename:** `16-03-group-policy-update.png`  
> **Description:** Command Prompt showing `gpupdate /force` used while testing the custom Group Policy.

> 📸 **Screenshot Placeholder**  
> **Filename:** `16-04-fsmo-query.png`  
> **Description:** Command Prompt showing `netdom query fsmo` and the FSMO role ownership on server01.

> 📸 **Screenshot Placeholder**  
> **Filename:** `16-05-event-viewer-troubleshooting.png`  
> **Description:** Event Viewer showing filtered Critical and Error events used for troubleshooting.

> 📸 **Screenshot Placeholder**  
> **Filename:** `16-06-file-share-test.png`  
> **Description:** server02 accessing the `\\server01\Projects` network share to verify file-server connectivity and access.

---

# Skills Demonstrated

This section demonstrates practical experience with:

- Windows Server troubleshooting
- Network connectivity testing
- DNS troubleshooting
- Active Directory troubleshooting
- Domain-join validation
- DHCP troubleshooting
- Group Policy troubleshooting
- FSMO role verification
- File Server troubleshooting
- SMB share validation
- Windows Firewall troubleshooting
- Event Viewer analysis
- Security event investigation
- PowerShell verification
- Command-line administration
- Recovery planning
- Structured problem solving

---

# Key Takeaway

The biggest lesson I took from this project is that troubleshooting becomes much easier when I understand how the different services depend on each other.

For example:

**Active Directory depends heavily on DNS.**

**DHCP provides clients with the network information they need.**

**Group Policy depends on the domain environment.**

**File sharing depends on network connectivity, permissions, and the SMB service.**

**Event Viewer provides evidence when something goes wrong.**

Instead of treating each problem as an isolated issue, I learned to work through the infrastructure layer by layer.

That mindset is one of the most valuable skills I gained from building this lab.

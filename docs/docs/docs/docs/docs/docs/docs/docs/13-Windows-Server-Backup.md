# Windows Server Backup

## Table of Contents

- [Overview](#overview)
- [Windows Server Backup in the Lab](#windows-server-backup-in-the-lab)
- [Backup Configuration](#backup-configuration)
- [Recovery Testing](#recovery-testing)
- [What the Source Documentation Confirms](#what-the-source-documentation-confirms)
- [Why Backup and Recovery Matter](#why-backup-and-recovery-matter)
- [Screenshots](#screenshots)
- [Skills Demonstrated](#skills-demonstrated)
- [Documentation Note](#documentation-note)

---

## Overview

Data protection was an important part of my Windows Server lab.

Throughout the project, I wanted the environment to represent more than just a collection of working services. I also wanted to understand how an administrator would protect server data and recover it if something went wrong.

Windows Server Backup is one of the features included in Windows Server that provides backup and recovery capabilities.

In my project, I configured **Windows Server Backup with scheduled backups and recovery testing**.

This allowed backup and recovery to become part of the overall server administration process rather than something considered only after a failure.

---

## Windows Server Backup in the Lab

Windows Server Backup was one of the Windows Server features used during the project.

Earlier in the project, I learned that Windows Server features provide additional tools and functionality that support the server's primary roles.

Windows Server Backup was specifically identified as providing:

- Scheduled backups.
- Recovery of server data.

The feature formed part of the wider infrastructure alongside:

- Active Directory Domain Services.
- DNS.
- DHCP.
- Group Policy.
- File Server.
- Storage Spaces.
- Windows Defender Firewall.
- Microsoft Defender Antivirus.
- Event Viewer.

This was important because protecting the server is not only about securing it against attacks. I also needed to consider what would happen if data was accidentally deleted, corrupted, or otherwise needed to be recovered.

---

## Backup Configuration

As part of the completed lab, I configured **Windows Server Backup with scheduled backups**.

The purpose was to establish a repeatable backup process rather than relying on manually creating backups only when I remembered to do so.

A scheduled backup provides a more consistent approach to protecting server data because the backup process can occur according to a defined schedule.

> 📸 **Screenshot Placeholder**  
> **Filename:** `13-01-windows-server-backup.png`  
> **Description:** Windows Server Backup management interface showing the configured backup environment.

### Backup Configuration Recorded in the Project

The original project documentation confirms the following:

| Item | Project Status |
|---|---|
| Windows Server Backup | Configured |
| Scheduled backups | Configured |
| Recovery testing | Completed |
| Backup and recovery | Included as part of the completed lab |

The source documentation does not provide the exact schedule, backup destination, retention configuration, or other detailed wizard values.

I have intentionally not added values that were not recorded in the original project.

---

## Recovery Testing

Backup configuration alone is not enough.

A backup is only useful if the data can actually be recovered when it is needed.

For this reason, the project included **recovery testing**.

The original documentation identifies Windows Server Backup as having been configured with scheduled backups and recovery testing, demonstrating that the project covered both sides of the process:

```text
Backup
   ↓
Scheduled protection
   ↓
Recovery testing
   ↓
Verify that data can be recovered
```

> 📸 **Screenshot Placeholder**  
> **Filename:** `13-02-backup-recovery-test.png`  
> **Description:** Evidence of the Windows Server Backup recovery testing performed during the lab.

Testing recovery was an important part of the exercise because simply assuming that a backup will work is not the same as actually verifying it.

---

## What the Source Documentation Confirms

The completed project summary records Windows Server Backup as one of the technologies successfully implemented.

The project documentation specifically states that I configured:

> **Windows Server Backup, configured to perform scheduled backups and successfully tested through a file recovery exercise.** 

This means the documented project outcome includes:

1. Windows Server Backup was configured.
2. Scheduled backups were configured.
3. Recovery testing was performed.
4. The recovery testing included a file recovery exercise.

These points are the exact backup-related details recorded in the source project.

---

## Why Backup and Recovery Matter

One of the main lessons from including backup functionality in the lab is that infrastructure availability is not only about keeping services running.

There are situations where data may need to be recovered, including:

- Accidental deletion.
- File corruption.
- System problems.
- Other situations where previously protected data needs to be restored.

A backup strategy therefore provides another layer of protection for the environment.

This also connects with the wider business scenario behind the project.

The lab was designed to simulate an organisation that needs reliable infrastructure, secure file sharing, centralised management, and protection of business information.

Without a structured backup strategy, an organisation could lose important data after an incident and have no reliable way to recover it.

---

## Backup as Part of the Wider Environment

Windows Server Backup was not treated as an isolated technology.

It formed part of the larger infrastructure I built:

```text
                    Windows Server 2025
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
   Active Directory       DNS                DHCP
        │                  │                  │
        └──────────────────┼──────────────────┘
                           │
                    File Server
                           │
                    Storage Spaces
                           │
                     Server Data
                           │
                  Windows Server Backup
                           │
                  Scheduled Protection
                           │
                    Recovery Testing
```

This helped me understand that system administration is about more than configuring individual technologies.

Each component contributes to the reliability and security of the overall environment.

---

## Screenshots

The following screenshots should be included in the repository's `screenshots/` directory.

| Filename | Description |
|---|---|
| `13-01-windows-server-backup.png` | Windows Server Backup management interface |
| `13-02-backup-recovery-test.png` | Evidence of the completed recovery testing |

> **Important:** The filenames above are repository placeholders. They should only be used once the corresponding screenshots from the actual project have been identified or captured.

---

## Skills Demonstrated

- Windows Server Backup
- Backup administration
- Scheduled backup configuration
- Recovery testing
- File recovery validation
- Data protection
- Server administration
- Backup and recovery fundamentals
- Infrastructure reliability

---


## Final Takeaway

Adding Windows Server Backup to the lab helped me understand that protecting an environment means planning for failure as well as preventing it.

The project did not stop at configuring server services. I also configured scheduled backups and tested recovery, giving me practical exposure to the importance of being able to recover data when it is needed.

That is an important part of responsible Windows Server administration: **a system is not fully protected simply because it is running correctly today; I also need to know how I would recover it when something goes wrong.**

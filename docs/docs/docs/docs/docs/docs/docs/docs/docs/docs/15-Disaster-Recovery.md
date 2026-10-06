# 15  Disaster Recovery

## Table of Contents

- [Overview](#overview)
- [Recovery Approach Used in the Lab](#recovery-approach-used-in-the-lab)
- [VMware Snapshot Recovery](#vmware-snapshot-recovery)
- [Windows Server Backup and Recovery Testing](#windows-server-backup-and-recovery-testing)
- [Storage Resilience](#storage-resilience)
- [Active Directory and FSMO Role Awareness](#active-directory-and-fsmo-role-awareness)
- [Recovery Considerations](#recovery-considerations)
- [What This Lab Demonstrated](#what-this-lab-demonstrated)
- [Screenshots](#screenshots)
- [Skills Demonstrated](#skills-demonstrated)
- [Documentation Limitation](#documentation-limitation)
- [Key Takeaway](#key-takeaway)

---

## Overview

Disaster recovery is the process of restoring systems, services, or data after something goes seriously wrong.

While building this Windows Server 2025 lab, I learned that recovery should not be treated as something to think about only after a failure happens.

The environment was designed so that I could experiment, make mistakes, troubleshoot problems, and recover from certain failures without having to rebuild everything from scratch.

Several parts of my project contributed to this approach:

- VMware snapshots
- Windows Server Backup
- Storage Spaces with parity
- File recovery testing
- Documentation of important Active Directory information
- Understanding which server held the FSMO roles

These features do not all provide the same type of protection. A VMware snapshot provides a virtual machine recovery point, Windows Server Backup provides backup and recovery capabilities, Storage Spaces provides storage resilience, and infrastructure documentation helps an administrator understand what needs to be recovered during an outage.

---

## Recovery Approach Used in the Lab

Because this was a home lab, I used virtualization to make experimentation safer.

The entire Windows Server environment was running inside **VMware Workstation** on my Windows 11 computer.

One of the advantages of this approach was that I could experiment with configurations without permanently committing every change.

If something went seriously wrong, I had recovery options available rather than having to immediately rebuild the entire environment.

My recovery-related approach included:

1. Creating a VMware snapshot before installing Windows Server.
2. Using Windows Server Backup for scheduled backups.
3. Testing recovery through a file recovery exercise.
4. Using Storage Spaces parity to provide resilience against a disk failure.
5. Documenting important Active Directory information, including FSMO role ownership.

---

## VMware Snapshot Recovery

Before powering on the Windows Server virtual machine for the first time, I created a VMware snapshot.

I used:

**VM → Snapshot → Take Snapshot**

I named the snapshot:

```text
Before Install
```

This created a clean recovery point before the Windows Server installation began.

The purpose was simple: if the installation failed or I wanted to restart the project from a clean state, I could return the virtual machine to this earlier point instead of creating an entirely new virtual machine.

### Why the Snapshot Was Useful

The snapshot gave me a safe starting point for the project.

Because I was learning and experimenting, there was always a possibility that a configuration change could cause an unexpected problem.

Having a known recovery point allowed me to experiment with more confidence.

The original project documentation also notes that VMware snapshots allowed me to roll the virtual machine back to a known working state within minutes.

> **Important:**  
> A VMware snapshot was used as a lab recovery mechanism. It should not be treated as a replacement for a proper backup strategy.

---

## Windows Server Backup and Recovery Testing

Windows Server Backup was included as part of the project.

The original project documentation confirms that I configured **Windows Server Backup with scheduled backups and recovery testing**.

The project summary also confirms that the backup configuration was successfully tested through a **file recovery exercise**.

This was important because creating backups is only part of a recovery strategy.

A backup that has never been tested does not give the same confidence as one that has successfully been used for recovery.

### What I Confirmed

The project confirms that I:

- Configured Windows Server Backup.
- Used scheduled backups.
- Performed recovery testing.
- Successfully completed a file recovery exercise.

The recovery exercise gave me practical experience with the idea that backup protection must be validated rather than simply assumed to work.

### Backup and Recovery Relationship

The basic process I learned was:

```text
Data
  ↓
Backup
  ↓
Scheduled Protection
  ↓
Recovery Test
  ↓
Verify Restored Data
```

This reinforced an important administrative lesson: **backup and recovery are connected processes**.

The objective is not simply to have backup jobs running. The objective is to be confident that important data can actually be recovered when required.

---

## Storage Resilience

Another part of the lab that contributed to resilience was **Windows Storage Spaces**.

I created a storage pool using five virtual hard disks and configured the virtual disk with **Parity**.

The resulting storage configuration was presented as the **Z:** drive.

The configuration was:

| Component | Configuration |
|---|---|
| Storage technology | Windows Storage Spaces |
| Physical virtual disks | 5 |
| Disk size | 10 GB each |
| Storage pool | `Lab Storage Pool` |
| Virtual disk | `Lab Virtual Disk` |
| Storage layout | Parity |
| Provisioning | Thin |
| Virtual disk size | 10 GB |
| Drive letter | `Z:` |
| File system | NTFS |
| Volume label | `Storage Pool` |

Parity provides resilience by distributing data and parity information across the disks.

This gave me practical experience with the idea that storage design can provide protection against certain disk failures.

### Verification

I verified the Storage Spaces configuration using PowerShell:

```powershell
Get-StoragePool
Get-VirtualDisk
Get-Volume
```

The expected configuration showed:

- `Lab Storage Pool`
- `Lab Virtual Disk`
- Parity resiliency
- Healthy operational status
- `Z:` as the resulting volume

> **Important:**  
> Storage resilience and backup are not the same thing. Storage Spaces parity helps protect against certain disk failures, while backups provide a separate recovery mechanism for lost or damaged data.

---

## Active Directory and FSMO Role Awareness

Active Directory recovery is another important consideration because the Domain Controller provides critical services to the environment.

In my lab, **server01** was the Domain Controller and held all five FSMO roles because the environment contained only one Domain Controller.

I checked the FSMO role ownership using:

```cmd
netdom query fsmo
```

The five FSMO roles were assigned to `server01`.

These roles are important to understand during an outage or disaster recovery situation because an administrator needs to know where critical Active Directory responsibilities are located.

One lesson I took from this was the importance of infrastructure documentation.

During an outage or disaster recovery scenario is the worst possible time to discover that I do not know which server holds critical Active Directory roles.

---

## Recovery Considerations

The lab helped me understand that disaster recovery involves more than one technology.

Different recovery mechanisms protect against different types of problems.

| Recovery Mechanism | Purpose in the Lab |
|---|---|
| VMware Snapshot | Provided a virtual machine recovery point |
| Windows Server Backup | Provided scheduled backup and recovery capability |
| File Recovery Exercise | Tested whether backed-up data could be recovered |
| Storage Spaces Parity | Provided resilient storage across five virtual disks |
| Active Directory Documentation | Helped identify critical domain infrastructure |
| FSMO Role Check | Identified the Domain Controller holding the five FSMO roles |

This helped me understand why administrators should not rely on a single recovery mechanism.

---

## What This Lab Demonstrated

By incorporating recovery into the project, I gained practical exposure to several important concepts.

### 1. Recovery Points Matter

The `Before Install` VMware snapshot gave me a known state that I could return to if the initial installation failed.

### 2. Backups Need Testing

The project did not stop at configuring Windows Server Backup. I also performed recovery testing through a file recovery exercise.

### 3. Storage Resilience Is Different from Backup

The Storage Spaces parity configuration helped protect against certain disk failures, but it serves a different purpose from a backup.

### 4. Infrastructure Documentation Matters

Knowing how the environment is structured, which server provides which service, and where important Active Directory roles are located can make recovery much easier.

### 5. Recovery Should Be Planned Before Failure

The most important lesson I took from this part of the project is that recovery should be considered during infrastructure design rather than after an outage occurs.

---

## Screenshots

> 📸 **Screenshot Placeholder**  
> **Filename:** `15-01-vmware-before-install-snapshot.png`  
> **Description:** VMware Workstation showing the `Before Install` snapshot created before the Windows Server installation.

> 📸 **Screenshot Placeholder**  
> **Filename:** `15-02-windows-server-backup.png`  
> **Description:** Windows Server Backup configuration showing the backup environment used in the project.

> 📸 **Screenshot Placeholder**  
> **Filename:** `15-03-file-recovery-test.png`  
> **Description:** Evidence of the file recovery exercise used to verify that backed-up data could be recovered.

> 📸 **Screenshot Placeholder**  
> **Filename:** `15-04-storage-spaces-recovery-resilience.png`  
> **Description:** Storage Spaces configuration showing the parity storage pool and virtual disk used in the lab.

> 📸 **Screenshot Placeholder**  
> **Filename:** `15-05-fsmo-role-verification.png`  
> **Description:** Command Prompt showing the `netdom query fsmo` command used to identify the FSMO role holders.

---

## Skills Demonstrated

This section demonstrates practical experience with:

- Disaster recovery concepts
- VMware snapshot management
- Windows Server Backup
- Backup recovery testing
- File recovery
- Windows Storage Spaces
- Parity-based storage
- Active Directory infrastructure awareness
- FSMO role identification
- Recovery planning
- Infrastructure documentation
- System resilience
- Troubleshooting and recovery

---

## Documentation Limitation

The original project document confirms that Windows Server Backup was configured with scheduled backups and that recovery testing was successfully performed through a file recovery exercise.

However, the source documentation does **not** provide the detailed backup configuration values such as:

- Backup destination
- Backup schedule
- Retention period
- Specific backup items
- Recovery wizard selections
- `wbadmin` commands
- PowerShell backup commands
- Backup media configuration

I have deliberately not invented these details for this repository.

The same principle applies to disaster recovery: this file documents the recovery mechanisms and lessons that are actually supported by the original project rather than creating a fictional disaster recovery procedure.

---

## Key Takeaway

This part of the project taught me that building infrastructure is only one side of system administration.

I also need to think about what happens when something goes wrong.

The combination of VMware snapshots, Windows Server Backup, recovery testing, Storage Spaces parity, and proper infrastructure documentation gave me practical exposure to different layers of resilience.

More importantly, I learned that a recovery plan is only useful when I understand **what is being protected, how it can be recovered, and how I know the recovery process actually works**.

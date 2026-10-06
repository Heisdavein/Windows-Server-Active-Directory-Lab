# Storage Spaces: Storage Pools, Parity Virtual Disk, and Z: Volume

## Table of Contents

- [Overview](#overview)
- [What I Built](#what-i-built)
- [Why I Used Storage Spaces](#why-i-used-storage-spaces)
- [Storage Configuration](#storage-configuration)
- [Step 1: Adding Virtual Disks in VMware](#step-1-adding-virtual-disks-in-vmware)
- [Step 2: Bringing the Disks Online](#step-2-bringing-the-disks-online)
- [Step 3: Creating a Storage Pool](#step-3-creating-a-storage-pool)
- [Step 4: Creating a Virtual Disk](#step-4-creating-a-virtual-disk)
- [Step 5: Creating and Formatting a Volume](#step-5-creating-and-formatting-a-volume)
- [Verifying the Storage Configuration with PowerShell](#verifying-the-storage-configuration-with-powershell)
- [Understanding the Final Configuration](#understanding-the-final-configuration)
- [Screenshots](#screenshots)
- [Skills Demonstrated](#skills-demonstrated)
- [What I Learned](#what-i-learned)

---

## Overview

As I continued building my Windows Server lab, I wanted to explore one of Windows Server's built-in storage technologies: **Storage Spaces**.

Storage Spaces allows multiple physical or virtual disks to be combined into a single **storage pool**. From that pool, I can create virtual disks with different storage layouts and levels of redundancy.

One of the things I found most interesting about Storage Spaces is that it provides functionality similar to a traditional hardware RAID controller, but through software.

For this exercise, I added **five virtual hard disks** to `server01`, combined them into a storage pool, created a **parity virtual disk**, and formatted it as a usable `Z:` volume.

This also gave me a practical way to learn resilient storage without needing additional physical hard drives.

---

## What I Built

The final storage configuration was:

| Component | Configuration |
|---|---|
| Server | `server01` |
| Additional disks | 5 |
| Disk capacity | 10 GB each |
| Total pool capacity | Approximately 50 GB |
| Storage pool | `Lab Storage Pool` |
| Virtual disk | `Lab Virtual Disk` |
| Storage layout | Parity |
| Provisioning | Thin Provisioning |
| Virtual disk size | 10 GB |
| Volume drive letter | `Z:` |
| Volume label | `Storage Pool` |
| File system | NTFS |

The five additional virtual disks were presented to Windows Server as separate disks and then combined into the `Lab Storage Pool`.

The resulting virtual disk used **Parity**, which provides fault tolerance similar to RAID 5.

---

# Why I Used Storage Spaces

In a traditional server environment, storage redundancy might be provided using a hardware RAID controller.

Storage Spaces provides a software-based alternative.

Instead of depending on a dedicated RAID controller, Windows Server can manage multiple disks and distribute data and parity information across them.

For this lab, that made Storage Spaces particularly useful because I could simulate a multi-disk storage environment using VMware virtual disks.

> **Lab Note:** In a production environment, these would typically be physical hard drives or SSDs installed inside a server. In my lab, they were virtual disks, but Windows Server manages them in essentially the same Storage Spaces workflow.

---

# Storage Configuration

The configuration consisted of five additional virtual disks:

```text
Disk 2 → 10 GB
Disk 3 → 10 GB
Disk 4 → 10 GB
Disk 5 → 10 GB
Disk 6 → 10 GB
```

Together, they provided approximately:

```text
50 GB
```

of raw storage for the storage pool.

The storage pool was named:

```text
Lab Storage Pool
```

The virtual disk created from that pool was named:

```text
Lab Virtual Disk
```

The virtual disk used:

```text
Parity
```

and:

```text
Thin Provisioning
```

The final volume was:

```text
Z:
```

with the label:

```text
Storage Pool
```

and the file system:

```text
NTFS
```

---

# Step 1: Adding Virtual Disks in VMware

Before Windows Server could use Storage Spaces, I first needed to provide it with additional storage.

I shut down `server01` and opened its VMware hardware configuration.

### Adding the First Disk

1. I shut down `server01`.
2. I right-clicked the virtual machine in VMware.
3. I selected **Settings**.
4. From the hardware settings window, I clicked **Add**.
5. I selected **Hard Disk**.
6. I continued through the wizard.
7. I created a new virtual hard disk with a capacity of:

```text
10 GB
```

8. I selected:

```text
Store virtual disk as a single file
```

9. I left:

```text
Allocate all disk space now
```

unchecked.

Leaving this option unchecked provides thin provisioning at the VMware virtual-disk level. The host does not immediately reserve the complete 10 GB; storage is consumed as data is written.

10. I accepted the default file name.
11. I clicked **Finish**.

I repeated the same process four more times until `server01` had a total of **five additional 10 GB virtual hard disks**.

The VMware configuration therefore contained the original 60 GB system disk plus five additional 10 GB disks.

> 📸 **Screenshot Placeholder**
>
> **Filename:** `storage-spaces-vmware-disks.png`
>
> **Description:** VMware Workstation hardware configuration for `server01`, showing the five additional 10 GB virtual disks alongside the original 60 GB system disk.

Once all five disks had been added, I clicked **OK** and powered the virtual machine back on.

---

# Step 2: Bringing the Disks Online

After Windows Server started, the newly added disks were detected automatically, but they were not yet ready for use.

1. I opened **Server Manager**.
2. I selected:

```text
File and Storage Services
```

3. Under **Disks**, I located the five newly added drives.

They appeared as:

```text
Offline
```

and:

```text
Unallocated
```

4. I right-clicked each disk individually.
5. I selected:

```text
Bring Online
```

6. I confirmed the warning message when prompted.
7. After bringing each disk online, I right-clicked each drive again.
8. I selected:

```text
Initialize
```

9. I selected:

```text
GPT
```

as the partition style.

GPT stands for **GUID Partition Table**. It is the modern partitioning standard used by Windows for managing disks.

At this point, all five disks were online, initialized, and available for Storage Spaces.

> 📸 **Screenshot Placeholder**
>
> **Filename:** `storage-spaces-disks-online.png`
>
> **Description:** Server Manager → File and Storage Services → Disks showing the five additional 10 GB disks online, unallocated, and using GPT.

---

# Step 3: Creating a Storage Pool

With the disks prepared, I was ready to create the storage pool.

1. Within **File and Storage Services**, I selected:

```text
Storage Pools
```

2. From the **Tasks** menu, I selected:

```text
New Storage Pool
```

3. The wizard detected `server01` as the available storage subsystem.
4. I selected `server01`.
5. I clicked **Next**.
6. I named the storage pool:

```text
Lab Storage Pool
```

7. I selected all five 10 GB disks.
8. I left the allocation setting as:

```text
Automatic
```

9. Before creating the pool, I reviewed the summary.

The summary showed approximately:

```text
50 GB
```

of total storage capacity.

10. Everything looked correct, so I clicked **Create**.

The storage pool was created successfully.

At this stage, the five individual disks had been grouped into one logical storage pool that could be used to create virtual disks.

> 📸 **Screenshot Placeholder**
>
> **Filename:** `storage-spaces-new-pool.png`
>
> **Description:** New Storage Pool Wizard showing all five 10 GB physical disks selected for `Lab Storage Pool`.

---

# Step 4: Creating a Virtual Disk

Immediately after creating the storage pool, Windows prompted me to create a virtual disk.

1. I left:

```text
Create a virtual disk when this wizard closes
```

enabled.

2. I continued through the wizard.
3. I named the virtual disk:

```text
Lab Virtual Disk
```

---

## Selecting the Storage Layout

On the **Storage Layout** page, I selected:

```text
Parity
```

I chose Parity because it provides fault tolerance similar to **RAID 5**.

Instead of storing every piece of data on a single disk, Windows distributes both data and parity information across the disks in the storage pool.

If one disk fails, the remaining data and parity information can be used to reconstruct the missing information.

> **Important:** The RAID 5 comparison is used here to explain the concept of the lab configuration. The implementation itself is Windows Storage Spaces Parity rather than a hardware RAID controller.

> 📸 **Screenshot Placeholder**
>
> **Filename:** `storage-spaces-parity-layout.png`
>
> **Description:** New Virtual Disk Wizard showing `Lab Virtual Disk` using the `Parity (like RAID 5)` storage layout.

---

## Selecting Thin Provisioning

For the provisioning method, I selected:

```text
Thin Provisioning
```

Thin provisioning allows the virtual disk to grow as data is written rather than immediately allocating all available storage.

This was consistent with the thin-provisioned VMware disks I created earlier.

---

## Setting the Virtual Disk Size

Finally, I specified a virtual disk size of:

```text
10 GB
```

I reviewed the configuration and clicked **Create**.

The virtual disk was created successfully.

---

# Step 5: Creating and Formatting a Volume

Although the virtual disk had been created, it still needed to be formatted before it could store files.

1. When prompted to create a volume, I left the option enabled.
2. I continued through the wizard.
3. I selected:

```text
Lab Virtual Disk
```

4. I clicked **Next**.
5. I accepted the default volume size so that the entire virtual disk would be available for use.
6. I assigned the drive letter:

```text
Z:
```

---

## File System Configuration

For the file system, I selected:

```text
NTFS
```

I then gave the volume the label:

```text
Storage Pool
```

The final configuration was therefore:

```text
Drive Letter: Z:
Volume Label: Storage Pool
File System: NTFS
```

Windows also provides ReFS, which offers additional resilience and modern storage features, but NTFS was sufficient for this lab.

7. I reviewed the configuration.
8. I clicked **Create**.

Once the wizard completed, I opened File Explorer and confirmed that the new `Z:` drive was available.

> 📸 **Screenshot Placeholder**
>
> **Filename:** `storage-spaces-new-volume.png`
>
> **Description:** New Volume Wizard showing `Z:` as the assigned drive letter and NTFS as the selected file system.

---

# Verifying the Storage Configuration with PowerShell

After completing the configuration through the graphical management tools, I wanted to verify that Windows Server recognised the storage pool, virtual disk, and volume correctly.

I opened PowerShell and ran:

```powershell
Get-StoragePool
```

This displayed the storage pool configuration.

The expected result from my lab was:

```text
FriendlyName      OperationalStatus  HealthStatus  Size
------------      -----------------  ------------  ----
Lab Storage Pool  OK                 Healthy       50.00 GB
```

I then ran:

```powershell
Get-VirtualDisk
```

The virtual disk appeared as:

```text
FriendlyName      ResiliencySettingName  OperationalStatus  HealthStatus  Size
------------      ---------------------  -----------------  ------------  ----
Lab Virtual Disk  Parity                 OK                 Healthy       10.00 GB
```

Finally, I ran:

```powershell
Get-Volume
```

The resulting volume information showed:

```text
DriveLetter  FileSystemLabel  FileSystem  HealthStatus  Size
-----------  ---------------  ----------  ------------  ----
Z            Storage Pool     NTFS        Healthy       9.76 GB
```

The lab output also showed approximately:

```text
9.52 GB
```

of remaining space on the volume at the time of verification.

> 📸 **Screenshot Placeholder**
>
> **Filename:** `storage-spaces-powershell-verification.png`
>
> **Description:** PowerShell showing `Get-StoragePool`, `Get-VirtualDisk`, and `Get-Volume`, confirming `Lab Storage Pool`, `Lab Virtual Disk`, Parity, and the healthy NTFS `Z:` volume.

---

# Understanding the Final Configuration

The final storage architecture can be represented as:

```text
VMware Workstation
        │
        ▼
     server01
        │
        ├── Original 60 GB system disk
        │
        ├── 10 GB virtual disk
        ├── 10 GB virtual disk
        ├── 10 GB virtual disk
        ├── 10 GB virtual disk
        └── 10 GB virtual disk
                 │
                 ▼
        Lab Storage Pool
             ~50 GB
                 │
                 ▼
          Lab Virtual Disk
              10 GB
             Parity
                 │
                 ▼
          NTFS Volume
              Z:
        Storage Pool
                 │
                 ▼
          File Server
        \\server01\Projects
```

This storage volume later became the foundation for the file-server configuration.

The `Projects` folder was created on:

```text
Z:\Projects
```

and shared over the network as:

```text
\\server01\Projects
```

This connected the storage configuration directly to the file-sharing portion of the project.

---

# Why the Parity Configuration Matters

One of the most useful parts of this exercise was seeing how the storage layers fit together.

I started with five separate virtual disks.

Windows then combined those disks into:

```text
Lab Storage Pool
```

From that pool, I created:

```text
Lab Virtual Disk
```

using:

```text
Parity
```

The virtual disk was then presented to Windows as a normal volume:

```text
Z:
```

and formatted with:

```text
NTFS
```

From the perspective of File Explorer, it behaved like a normal drive.

However, underneath that drive letter, Windows Storage Spaces was managing the storage pool and parity configuration.

That helped me understand the difference between the **physical storage layer**, the **storage-pool layer**, the **virtual-disk layer**, and the **file-system layer**.

---

# Storage Layers in the Lab

The complete process can be viewed as four main layers:

### 1. Physical/Virtual Disk Layer

Five VMware virtual hard disks:

```text
5 × 10 GB
```

### 2. Storage Pool Layer

The disks were combined into:

```text
Lab Storage Pool
```

with approximately:

```text
50 GB
```

of total capacity.

### 3. Virtual Disk Layer

A virtual disk was created:

```text
Lab Virtual Disk
```

with:

```text
Parity
Thin Provisioning
10 GB
```

### 4. Volume/File-System Layer

The virtual disk was formatted as:

```text
NTFS
```

and assigned:

```text
Z:
```

with the volume label:

```text
Storage Pool
```

This layered approach made the Storage Spaces architecture much easier for me to understand.

---

# Screenshots

The following screenshots should be included in the repository to document the Storage Spaces implementation:

| Filename | Description |
|---|---|
| `storage-spaces-vmware-disks.png` | VMware hardware configuration showing the five additional 10 GB virtual disks. |
| `storage-spaces-disks-online.png` | Server Manager showing the five disks online and initialized with GPT. |
| `storage-spaces-new-pool.png` | New Storage Pool Wizard showing all five disks selected. |
| `storage-spaces-parity-layout.png` | New Virtual Disk Wizard showing the Parity storage layout. |
| `storage-spaces-new-volume.png` | New Volume Wizard showing `Z:` and NTFS configuration. |
| `storage-spaces-powershell-verification.png` | PowerShell verification using `Get-StoragePool`, `Get-VirtualDisk`, and `Get-Volume`. |
| `storage-spaces-z-drive.png` | File Explorer showing the completed `Storage Pool (Z:)` volume. |

> 📸 **Screenshot Placeholder**
>
> **Filename:** `storage-spaces-z-drive.png`
>
> **Description:** File Explorer → This PC showing the completed `Storage Pool (Z:)` drive alongside the system `C:` drive.

---

# Skills Demonstrated

Through this section, I demonstrated practical experience with:

- VMware Workstation
- Virtual disk creation
- Thin provisioning
- Windows Server Storage Spaces
- Server Manager
- Disk management
- Bringing disks online
- GPT initialization
- Storage pool creation
- Virtual disk creation
- Parity storage
- RAID 5 concepts
- Thin-provisioned virtual disks
- NTFS formatting
- Volume management
- PowerShell storage commands
- `Get-StoragePool`
- `Get-VirtualDisk`
- `Get-Volume`
- Storage health verification
- Windows Server file-storage architecture

---

# What I Learned

Completing this exercise gave me a much better understanding of how Windows Server can provide enterprise-style storage management without requiring dedicated RAID hardware.

The most important lesson was understanding that Storage Spaces is not simply another type of disk.

It creates a layered storage system where multiple disks can be combined into a pool, virtual disks can be created from that pool, and those virtual disks can then be formatted and used by Windows like normal storage.

I also learned how **Parity** provides fault tolerance by distributing parity information across the disks in the pool.

Using PowerShell alongside the graphical management tools was also useful. The graphical interface made the configuration easier to understand visually, while commands such as:

```powershell
Get-StoragePool
Get-VirtualDisk
Get-Volume
```

gave me a quick way to verify the underlying configuration.

Most importantly, I could see how this storage configuration connected to the next stage of the project. The `Z:` volume became the storage location for my `Projects` file share, allowing the Storage Spaces work to become part of the wider Windows Server environment rather than existing as an isolated exercise.

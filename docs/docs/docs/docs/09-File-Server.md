# File Server: Shared Folders, NTFS Permissions, and Network Drives

## Table of Contents

- [Overview](#overview)
- [What I Built](#what-i-built)
- [Prerequisites](#prerequisites)
- [Step 1: Verifying the File Server Role](#step-1-verifying-the-file-server-role)
- [Step 2: Creating the Shared Folder](#step-2-creating-the-shared-folder)
- [Step 3: Sharing the Folder Using Server Manager](#step-3-sharing-the-folder-using-server-manager)
- [Step 4: Testing the Shared Folder from server02](#step-4-testing-the-shared-folder-from-server02)
- [Step 5: Mapping the Shared Folder as a Network Drive](#step-5-mapping-the-shared-folder-as-a-network-drive)
- [NTFS Permissions](#ntfs-permissions)
- [Understanding NTFS Permission Levels](#understanding-ntfs-permission-levels)
- [Understanding Permission Inheritance](#understanding-permission-inheritance)
- [Restricting Access to a Specific Folder](#restricting-access-to-a-specific-folder)
- [Verifying Permissions](#verifying-permissions)
- [Security Group-Based Access](#security-group-based-access)
- [What I Learned](#what-i-learned)
- [Screenshots](#screenshots)
- [Skills Demonstrated](#skills-demonstrated)

---

## Overview

One of the most common responsibilities of a Windows Server is acting as a **File Server**. Organisations rely on file servers to provide a central location where employees can securely store, access, and share documents, spreadsheets, project files, and departmental resources.

In this section, I configured **server01** to function as a file server by creating a shared folder on the **Z:** drive that I created using Storage Spaces. I then configured permissions, tested access from **server02**, and mapped the shared folder as a network drive.

This exercise gave me practical experience with:

- Windows Server file sharing
- SMB shares
- Server Manager
- NTFS permissions
- Active Directory security groups
- Access-Based Enumeration
- UNC paths
- Network drive mapping
- Permission inheritance
- Permission testing with `icacls`

The result was a working file-sharing environment where a domain-joined server could access centrally stored files on another server.

---

## What I Built

The file-server configuration used the following components:

| Component | Configuration |
|---|---|
| File server | `server01` |
| Client/server used for testing | `server02` |
| Storage location | `Z:` |
| Shared folder | `Z:\Projects` |
| Share name | `Projects` |
| Share description | `Projects folder – Staff access` |
| Sharing protocol | SMB |
| Share profile | SMB Share – Quick |
| Network path | `\\server01\Projects` |
| Network drive | `Z:` |
| Security group | `Air Staff` |
| Domain group | `Domain Users` |
| Restricted folder | `Air Staff Only` |
| Restricted-folder permission | `Modify` |
| Access-Based Enumeration | Enabled |

The `Z:` drive was the Storage Spaces volume created earlier in the project. This meant the file server was using the storage infrastructure I had already configured rather than relying on the operating system's primary disk.

---

## Prerequisites

Before configuring the file server, I already had:

- `server01` running Windows Server 2025.
- `server02` running Windows Server 2025.
- The `corp.danieltraining.com` Active Directory domain.
- `server02` joined to the domain.
- Active Directory security groups including `Air Staff`.
- Storage Spaces configured on `server01`.
- A usable `Z:` volume formatted with NTFS.

The Storage Spaces configuration created a parity virtual disk and presented the resulting usable volume as `Z:`.

---

# Step 1: Verifying the File Server Role

Before creating any shared folders, I first confirmed that the **File Server** role was installed.

1. I opened **Server Manager**.
2. I selected **Manage**.
3. I clicked **Add Roles and Features**.
4. I navigated to:

   **Server Roles → File and Storage Services → File and iSCSI Services**

5. The **File Server** role was already selected, which is the default on most Windows Server installations.
6. If it had not been installed, I would have selected it and completed the installation.
7. After confirming that the role was available, I closed the wizard.

At this point, `server01` was ready to provide file-sharing services.

> **Note:** Windows Server allows the same server to perform multiple roles. In this lab, `server01` already provided Active Directory and DNS services, and I later added file-server functionality.

---

# Step 2: Creating the Shared Folder

With the File Server role available, I created the folder that would be shared across the network.

1. I opened **File Explorer**.
2. I navigated to the `Z:` drive.

The `Z:` drive was the Storage Spaces volume I had created in the previous section.

3. Inside the drive, I created a new folder named:

```text
Projects
```

The resulting folder path was:

```text
Z:\Projects
```

This folder would serve as the central shared location that users on other domain-joined computers could access through the network.

---

# Step 3: Sharing the Folder Using Server Manager

Although Windows allows folders to be shared directly through File Explorer, I chose to use **Server Manager**.

I found this approach more structured and closer to the way file shares are typically managed in professional environments because it provides more configuration options and better visibility over existing shares.

### Creating the SMB Share

1. In **Server Manager**, I selected:

   **File and Storage Services → Shares**

2. From the **Tasks** menu, I selected **New Share**.
3. For the share type, I selected:

```text
SMB Share – Quick
```

SMB is the standard Windows file-sharing protocol used to allow computers to access shared files and folders across a network.

4. Rather than selecting one of the suggested locations, I selected **Type a custom path**.
5. I browsed to:

```text
Z:\Projects
```

6. After selecting the folder, I clicked **Next**.
7. The share name automatically populated as:

```text
Projects
```

I left the share name unchanged.

8. I added the following description:

```text
Projects folder – Staff access
```

---

## Enabling Access-Based Enumeration

On the **Other Settings** page, I enabled:

```text
Access-Based Enumeration
```

Access-Based Enumeration controls what users can see inside a shared folder.

When enabled, users only see folders and files they have permission to access. This is useful because users do not have to see resources that they cannot actually open.

In this lab, this feature would later be important when I created the restricted **Air Staff Only** folder.

---

## Configuring Permissions

On the **Permissions** page, I selected:

**Customize Permissions**

In the NTFS permissions window, I added:

```text
Domain Users
```

I granted the appropriate permissions, including:

- Read
- Write
- List Folder Contents

After reviewing the configuration, I clicked:

**Apply → Next → Create**

The `Projects` folder was now available as a network share.

---

## Verifying the Share

After creating the share, I verified it through:

**Server Manager → File and Storage Services → Shares**

The share appeared with the following configuration:

| Setting | Value |
|---|---|
| Share Name | `Projects` |
| Folder Path | `Z:\Projects` |
| Protocol | SMB |
| Availability Type | Not Clustered |
| Status | Online |

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/2caab20d-06d8-45aa-b1a2-08bb3a65fae8" />
> **Description:** Server Manager showing the `Projects` SMB share, its `Z:\Projects` folder path, SMB protocol, Not Clustered availability type, and Online status.

---

# Step 4: Testing the Shared Folder from server02

Once the share had been created, I wanted to verify that another domain-joined server could access it successfully.

1. I switched to **server02**.
2. I opened **File Explorer**.
3. In the address bar, I entered the following UNC path:

```text
\\server01\Projects
```

A **UNC path**, or Universal Naming Convention path, provides a standard way of accessing a shared resource on another computer without needing to know its local drive letter.

The shared folder opened successfully.

To confirm that I had the correct permissions, I created a small test file inside the folder.

I then returned to `server01` and opened:

```text
Z:\Projects
```

The test file created from `server02` appeared immediately.

This confirmed that:

- The SMB share was working.
- `server02` could reach `server01`.
- The shared folder was accessible over the network.
- The configured permissions allowed the test file to be created.
- Both servers were accessing the same centrally stored data.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/b3c73b11-debe-4f8b-8995-0ea11d007d34" />
> **Description:** File Explorer on `server02` accessing `\\server01\Projects` and showing the shared folder contents.

---

# Step 5: Mapping the Shared Folder as a Network Drive

Although accessing a shared folder using its UNC path works perfectly, typing the full path every time can become inconvenient.

To make access easier, I mapped the shared folder as a network drive.

1. On `server02`, I opened **File Explorer**.
2. I right-clicked **This PC**.
3. I selected **Map Network Drive**.
4. I chose the drive letter:

```text
Z:
```

5. In the **Folder** field, I entered:

```text
\\server01\Projects
```

6. I selected:

```text
Reconnect at sign-in
```

This tells Windows to automatically recreate the drive mapping whenever I sign in.

7. Finally, I clicked **Finish**.

The shared folder then appeared in File Explorer on `server02` as the `Z:` drive.

Even though it looked like a locally attached disk, the data was actually stored on `server01`.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/9df01bb5-36f8-4ff4-9666-23c8898846c7" />
> **Description:** The Map Network Drive dialog showing drive `Z:`, folder `\\server01\Projects`, and Reconnect at sign-in enabled.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/e706c695-bda6-4b57-b95e-ba084e186f61" />

> **Description:** File Explorer on `server02` showing the shared `Projects` folder as the `Z:` network drive.

---

## Why Network Drive Mapping Matters

In a larger organisation, administrators would not normally configure drive mappings manually for every user.

Instead, the process could be automated through **Group Policy**, allowing network drives to be mapped automatically whenever users sign in.

Seeing how simple the manual process was made it easier for me to appreciate how powerful Group Policy can be when managing hundreds or even thousands of computers.

---

# NTFS Permissions

After creating the shared folder, the next step was learning how to control who could access it and what they were allowed to do.

This is where **NTFS permissions** become important.

NTFS permissions provide detailed access control for files and folders stored on an NTFS-formatted drive. They allow administrators to determine which actions users can perform, from simply reading files to modifying or deleting them.

I found NTFS permissions to be one of the most important concepts in Windows Server administration because they are commonly used in real environments and are frequently discussed during technical interviews.

---

## Understanding NTFS Permission Levels

Windows provides several standard NTFS permission levels.

| Permission | What It Allows |
|---|---|
| **Full Control** | Complete control over files and folders, including reading, writing, modifying, deleting, changing permissions, and taking ownership. |
| **Modify** | Allows users to read, edit, create, and delete files, but not change security permissions. |
| **Read & Execute** | Allows users to open files and run applications without making changes. |
| **List Folder Contents** | Allows users to view the contents of a folder without modifying them. |
| **Read** | Allows users to open and read files but prevents modifications. |
| **Write** | Allows users to create new files and edit existing ones, but not delete them. |

Understanding the differences between these permission levels helped me understand how organisations can protect sensitive information while still allowing employees to perform their jobs.

---

# Understanding Permission Inheritance

Another important concept I learned was **permission inheritance**.

By default, every file and folder inside Windows inherits its permissions from its parent folder. This saves administrators from having to configure permissions individually for every file or subfolder.

For example, if the `Projects` folder grants the `Domain Users` group Read access, folders created inside `Projects` can automatically receive the same permissions unless inheritance is changed.

This makes administration much simpler because permissions remain consistent throughout the folder structure.

However, there are situations where a specific folder needs tighter security than its parent. In those cases, inheritance can be disabled so that custom permissions can be applied.

---

# Restricting Access to a Specific Folder

To see how this worked in practice, I created a folder that only members of the **Air Staff** security group would be able to access.

## Creating the Restricted Folder

1. Inside the `Projects` folder, I created a new subfolder named:

```text
Air Staff Only
```

2. I right-clicked the folder.
3. I selected **Properties**.
4. I opened the **Security** tab.
5. I clicked **Advanced**.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/93d127af-2942-4ae5-b581-e1bd4eadc0ae" />
> **Description:** The `Air Staff Only Properties` Security tab showing the folder path `Z:\Projects\Air Staff Only` and the existing security principals.

---

## Disabling Permission Inheritance

6. In **Advanced Security Settings**, I selected:

```text
Disable inheritance
```

7. Windows asked how I wanted to handle the existing permissions.

Rather than removing everything, I selected:

```text
Convert inherited permissions into explicit permissions
```

This copied the existing permissions into the folder, giving me a safe starting point without having to recreate every permission manually.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/d3a20a3f-e79c-4518-82b5-ad713e38f7c1" />

> **Description:** Advanced Security Settings for `Z:\Projects\Air Staff Only`, showing Disable inheritance and the converted explicit permissions.

---

## Removing Domain Users Access

8. I removed the:

```text
Domain Users
```

group from the permissions list.

This was important because I wanted the folder to be restricted to members of the `Air Staff` security group.

---

## Granting Air Staff Modify Access

9. I clicked **Add**.
10. I selected the **Air Staff** security group.
11. I granted it:

```text
Modify
```

12. I configured the permission to apply to:

```text
This folder, subfolders and files
```

13. I reviewed the settings.
14. I clicked **Apply**.
15. I clicked **OK**.

The resulting permission allowed the `Air Staff` group to modify the contents of the folder without giving the group Full Control.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/0f5094d1-5538-439d-b40a-0fcaa498a87e" />

> **Description:** Permission Entry for `Air Staff Only`, showing `Air Staff (CORP\Air Staff)`, Allow, Modify, and Applies to: This folder, subfolders and files.

The effective permission configuration included:

- Full control: not granted
- Modify: granted
- Read & execute: granted
- List folder contents: granted
- Read: granted
- Write: granted

---

## Result of the Permission Change

Once the permissions were applied, only members of the **Air Staff** security group could access the folder.

Because I had previously enabled **Access-Based Enumeration** on the network share, users who were not members of the Air Staff group could not even see that the `Air Staff Only` folder existed.

From their perspective, the folder simply did not appear inside the shared folder.

This provided an additional layer of security while also reducing confusion for users.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/f0595a9f-097a-4440-bf33-d2bb2052a0dc" />
> **Description:** File Explorer showing the Projects folder from a user without Air Staff access, demonstrating that the restricted folder is not available to the user.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/b14d3e5a-20b2-4c32-92ba-0940deb050d8" />

> **Description:** File Explorer showing the `Air Staff Only` folder visible to a member of the Air Staff security group.

---

# Verifying Permissions

I also used `icacls` to verify the permissions assigned to the restricted folder.

The command I used was:

```cmd
icacls \\server01\Projects\Air Staff Only
```

The permissions output showed the `Air Staff` group with Modify access:

```text
AIR STAFF\:(OI)(CI)M
```

I also used:

```cmd
whoami
```

to confirm the account under which the test was being performed.

The lab output showed:

```text
corp\administrator
```

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/d9f03197-b23b-469a-ae53-52be266134f2" />
> **Description:** Command Prompt showing `whoami` and `icacls \\server01\Projects\Air Staff Only`, including the Air Staff Modify permission entry.

Using `icacls` gave me another way to verify the configuration instead of relying only on the graphical interface.

---

# Security Group-Based Access

One of the most valuable lessons I took away from this exercise was a best practice followed in most professional environments:

> **Never assign permissions directly to individual user accounts whenever it can be avoided. Instead, assign permissions to security groups.**

Using security groups makes administration much more efficient.

For example, if an employee joins a department, I only need to add them to the appropriate security group. If they leave the company or move to another role, I simply remove them from that group.

The folder permissions never need to be changed because access is managed through group membership rather than individual accounts.

In this lab, the relevant security group was:

```text
Air Staff
```

This group had already been created in Active Directory, and **Sarah White** had been added to it.

The approach demonstrated the relationship between:

```text
Active Directory User
        ↓
Security Group
        ↓
NTFS Permission
        ↓
File/Folder Access
```

This is a much more scalable approach than assigning permissions separately to every user.

---

# What I Learned

By completing this section, I gained a much clearer understanding of how Windows controls access to files and folders.

The project demonstrated how several Windows technologies work together:

- **Active Directory** manages identities and security groups.
- **Security groups** provide a scalable way to assign access.
- **SMB** provides network file sharing.
- **Share permissions** control access to the network share.
- **NTFS permissions** provide detailed access control over files and folders.
- **Permission inheritance** reduces administrative effort.
- **Access-Based Enumeration** hides resources users are not authorised to access.
- **Network drive mapping** makes shared resources easier for users to access.
- **Storage Spaces** provided the underlying `Z:` storage used by the file server.

One of the biggest lessons was that file-server security is not just about creating a folder and clicking Share. The permissions model matters just as much as the share itself.

I also learned why security groups are preferred over assigning permissions directly to individual users. The group becomes the point where access is managed, making the environment easier to maintain as the number of users grows.

By completing this section, I had successfully transformed `server01` into a functioning file server. Users on other domain-joined machines could access shared resources over the network, store files centrally, and work with them as though they were located on their own computers.

This is one of the core services provided by Windows Server in many enterprise environments, and implementing it in my lab gave me valuable hands-on experience with file sharing, permissions management, and network drive mapping.

---

# Screenshots
<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/4877db15-03d4-417b-9aeb-90f1d496bbaa" />
Server Manager showing the Projects SMB share configuration and the local folder path (Z:\Projects)

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/7b56924e-88ab-4ada-a22c-11a7d3942af1" />
Server02 accessing the Projects shared folder using the UNC path (\\server01\Projects) in File Explorer.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/a6c69b5e-4a05-4aef-858f-a8fbf53a6b97" />
Map Network Drive dialog configuring the Projects share as the Z: network drive.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/a9d3cd57-5755-4d1e-b3dd-7344a0450531" />
Server02 displaying the successfully mapped Z: network drive connected to the Projects share.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/f5977a14-b624-4224-9fb8-f19b22fa4cc0" />
Folder Properties → Security tab for the Air Staff Only folder.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/f2cedfa9-4faa-4a0a-ba26-9e1b3073dc3e" />
Advanced Security Settings showing NTFS permission inheritance being disabled.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/b32e41d1-c321-4649-b448-ba6f0ba9973c" />
NTFS permission entry granting the Air Staff security group Modify access.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/68198304-11b7-43ad-9bcb-a75a046754df" />
User without the required permissions unable to see the restricted folder because of Access-Based Enumeration (ABE)

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/29d5f703-7a46-440a-b5a4-d5774511a0f5" />
Air Staff user successfully accessing the restricted folder after NTFS permissions are applied.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/8b5ca300-6c9b-41ef-a010-bc014d745978" />
Command Prompt verifying user identity and NTFS permissions using whoami and icacls.









---

# Skills Demonstrated

Through this section, I demonstrated practical experience with:

- Windows Server 2025 File Server administration
- Server Manager
- File and Storage Services
- SMB file sharing
- Shared-folder configuration
- UNC paths
- Network drive mapping
- NTFS permissions
- Permission inheritance
- Access-Based Enumeration
- Active Directory security groups
- Security group-based access control
- Permission troubleshooting
- `icacls`
- Windows File Explorer
- Cross-server file access
- Storage Spaces integration with file services

---

## Key Takeaways

- **NTFS permissions** control access to files and folders.
- **Share permissions** control access over the network.
- **Inheritance** saves time and keeps permissions consistent.
- **Security groups** are preferable to assigning permissions directly to individual users.
- **Access-Based Enumeration** can hide folders from users who are not authorised to access them.
- **Network drive mapping** makes centrally stored resources easier for users to access.
- Combining **Active Directory, security groups, NTFS permissions, SMB, and Storage Spaces** creates a practical Windows Server file-sharing environment.

# Active Directory Domain Services

## Overview

After configuring `server01` with a static IP address and completing the initial Windows Server configuration, I moved on to building the Active Directory environment.

The goal was to turn `server01` into the Domain Controller for my lab and create a structured Active Directory environment containing organisational units, user accounts, security groups, and computer objects.

The domain I created was:

```text
corp.danieltraining.com
```

Active Directory became the central point for managing identities, computers, security groups, and Group Policy throughout the lab.

Microsoft describes Active Directory Domain Services (AD DS) as a directory service that provides a structured way to store and manage network objects such as users, computers, and resources.

---

## Active Directory Environment

| Component | Configuration |
|---|---|
| Domain Controller | `server01` |
| IP Address | `192.168.1.222` |
| Active Directory Domain | `corp.danieltraining.com` |
| DNS Server | `server01` |
| Second Server | `server02` |
| Second Server IP | `192.168.1.223` |
| Domain Administrator | `corp\administrator` |

`server01` became the Domain Controller and also provided DNS services for the domain.

---

## 1. Creating the Active Directory Environment

Once `server01` had been prepared, I configured Active Directory Domain Services and established the `corp.danieltraining.com` domain.

This changed the server from a standalone Windows Server into the central identity server for my lab.

From this point forward, users, computers, groups, and policies could be managed centrally instead of configuring every machine independently.

The Domain Controller also became an important dependency for the rest of the environment because other services and domain-joined computers needed to locate it through DNS.

---

## 2. Active Directory Users and Computers

After the domain controller was configured, I opened **Active Directory Users and Computers** from:

```text
Server Manager
→ Tools
→ Active Directory Users and Computers
```

I expanded:

```text
corp.danieltraining.com
```

Windows had already created several default containers. Instead of modifying those default containers, I created my own organisational structure.

---

## 3. Creating the Organisational Unit Structure

I wanted the Active Directory environment to be organised rather than placing every object into the default containers.

I created the following structure:

```text
corp.danieltraining.com
└── Daniel Training
    ├── End Users
    ├── Security Groups
    └── Servers
```

### Daniel Training

I created an OU named:

```text
Daniel Training
```

I used this as the main container for the custom Active Directory structure.

I also left **Protect container from accidental deletion** enabled before clicking **OK**.

### End Users

Inside `Daniel Training`, I created:

```text
End Users
```

This OU would contain the user accounts created for the lab.

### Security Groups

I created:

```text
Security Groups
```

This OU would contain the security groups used to manage access and permissions.

### Servers

Finally, I created:

```text
Servers
```

This OU would be used to organise Windows Server computer accounts that joined the domain.

The structure was simple, but it gave me a logical way to separate users, groups, and servers.

> **Note:** There is no single correct OU structure for every organisation. OUs can be organised around departments, locations, business functions, or other administrative requirements. The important part is having a structure that remains logical and manageable.

---

## 4. Creating User Accounts

With the OU structure in place, I moved into the `End Users` OU.

I selected:

```text
Daniel Training
└── End Users
```

I then created the user accounts from:

```text
New → User
```

The users I created were:

| User | Email Address |
|---|---|
| Emma Johnson | — |
| James Smith | `james.smith@corp.danieltraining.com` |
| Sarah White | `sarah.white@corp.danieltraining.com` |

For the new accounts, I created a temporary password:

```text
Welcome@123
```

I also enabled:

```text
User must change password at next logon
```

This meant the temporary password was not intended to become the user's permanent password.

### Why require a password change?

In a real organisation, administrators may create accounts using temporary credentials. Requiring the user to change the password during their first sign-in means the administrator does not need to know or retain the user's permanent password.

---

## 5. Adding Additional User Information

Creating the account was only part of the exercise.

I opened the properties of **James Smith** and explored the additional information that can be stored against a user account.

### General

I added information including:

- Description identifying James as an **IT Administrator**
- Office location
- Telephone number

### Account

I reviewed the account settings and noted that an account expiry date can be configured.

This can be useful for temporary accounts belonging to contractors, interns, or other users who should automatically lose access after a specific date.

### Organisation

I entered organisational information including:

- Job title
- Department
- Company
- Manager

For this exercise, I assigned:

```text
Manager: Emma Johnson
```

### Member Of

I also reviewed the **Member Of** tab.

At this stage, James was not yet a member of any of my custom security groups because those groups had not been created.

---

## 6. Creating Security Groups

The next step was creating security groups.

Security groups allow permissions to be assigned to groups rather than individual users. This makes access management easier because users can be added to or removed from groups without changing the underlying resource permissions.

I selected:

```text
Daniel Training
└── Security Groups
```

I then created two security groups.

### Air Staff

```text
Group Name: Air Staff
Group Scope: Global
Group Type: Security
```

### IT Admins

```text
Group Name: IT Admins
Group Scope: Global
Group Type: Security
```

Both groups were created with:

```text
Group Scope: Global
Group Type: Security
```

---

## 7. Adding Users to Security Groups

I used two different methods to assign users to groups.

### Method A — Add a User from the Group

First, I opened the properties of:

```text
IT Admins
```

I selected the **Members** tab and clicked **Add**.

I searched for:

```text
James Smith
```

I selected James and confirmed the change.

The resulting membership was:

```text
IT Admins
└── James Smith
```

### Method B — Add a Group from the User Account

Next, I opened the properties of:

```text
Sarah White
```

I selected the **Member Of** tab and clicked **Add**.

I searched for:

```text
Air Staff
```

I selected the group, clicked **OK**, and then **Apply**.

The resulting membership was:

```text
Air Staff
└── Sarah White
```

### Final Group Membership

At the end of this exercise, the custom group memberships were:

| User | Security Group |
|---|---|
| James Smith | IT Admins |
| Sarah White | Air Staff |
| Emma Johnson | None |

This gave the lab a much more realistic identity structure and created the foundation for later permission management and Group Policy configuration.

---

## 8. Joining Server02 to the Active Directory Domain

After creating the Active Directory structure, I created a second Windows Server virtual machine named:

```text
server02
```

The purpose was to have another Windows Server that could later perform additional roles, including file-server functionality.

### Server02 Network Configuration

I configured server02 with:

```text
Computer Name: server02
IP Address: 192.168.1.223
Preferred DNS: 192.168.1.222
```

The preferred DNS server pointed to `server01`, which was the Domain Controller and DNS server.

---

## 9. Testing DNS Before the Domain Join

Before joining server02 to the domain, I wanted to confirm that it could locate the Domain Controller.

From server02, I opened Command Prompt and ran:

```cmd
ping server01.corp.danieltraining.com
```

The hostname successfully resolved to the IP address of `server01`, and I received reply packets.

This confirmed that:

- DNS resolution was working.
- server02 could locate server01.
- Network connectivity between the two servers was working.

This was an important check before attempting the domain join.

---

## 10. Joining Server02 to the Domain

From server02, I opened:

```text
Server Manager
→ Local Server
```

I selected the **Workgroup** link to open System Properties.

I then selected **Change** and chose:

```text
Member of:
Domain
```

I entered:

```text
corp.danieltraining.com
```

Windows then requested domain credentials.

I entered:

```text
Username: corp\administrator
Password: Domain Administrator password
```

Windows successfully joined the server to the domain and displayed:

```text
Welcome to the corp.danieltraining.com domain.
```

I acknowledged the message and restarted server02.

---

## 11. Verifying Server02 in Active Directory

After server02 restarted, I returned to `server01`.

I opened:

```text
Server Manager
→ Tools
→ Active Directory Users and Computers
```

Under the domain, I opened the default **Computers** container and found:

```text
server02
```

The computer object had been created automatically when the server joined the domain.

To keep the environment organised, I moved `server02` from the default **Computers** container into:

```text
Daniel Training
└── Servers
```

This gave me a dedicated location for server computer objects.

---

## 12. Logging Into Server02 with Domain Credentials

After the restart, I selected **Other User** on the Windows login screen.

I signed in using:

```text
Username: corp\administrator
Password: Domain Administrator password
```

After logging in, I opened:

```text
Server Manager
→ Local Server
```

Instead of showing `WORKGROUP`, server02 now displayed:

```text
corp.danieltraining.com
```

This confirmed that the domain join had completed successfully.

At this stage, the lab had developed from a standalone Windows Server into a small domain environment containing:

```text
server01
└── Domain Controller
    ├── Active Directory
    └── DNS

server02
└── Domain-joined Windows Server
```

---

## 13. Checking the FSMO Roles

As part of learning how Active Directory operates internally, I also checked the **Flexible Single Master Operations (FSMO)** roles.

FSMO roles are specialised Active Directory responsibilities that are assigned to individual Domain Controllers.

I opened Command Prompt and ran:

```cmd
netdom query fsmo
```

Because my lab contained only one Domain Controller, all five FSMO roles were assigned to:

```text
server01
```

The five roles are:

1. Schema Master
2. Domain Naming Master
3. RID Master
4. PDC Emulator
5. Infrastructure Master

The important result from my lab was that `server01` held all five roles.

In a larger Active Directory environment with multiple Domain Controllers, FSMO roles can be distributed across Domain Controllers for operational and resilience considerations.

---

## 14. Active Directory Validation

I used several checks throughout the configuration to verify that Active Directory was functioning correctly.

### Domain Name

```text
corp.danieltraining.com
```

### Domain Controller

```text
server01
192.168.1.222
```

### Domain-Joined Server

```text
server02
192.168.1.223
```

### DNS Connectivity Test

```cmd
ping server01.corp.danieltraining.com
```

### FSMO Verification

```cmd
netdom query fsmo
```

### Active Directory Objects

I verified that:

- The `Daniel Training` OU existed.
- `End Users` existed.
- `Security Groups` existed.
- `Servers` existed.
- User accounts had been created.
- `Air Staff` and `IT Admins` existed.
- James Smith was a member of `IT Admins`.
- Sarah White was a member of `Air Staff`.
- server02 appeared in Active Directory.
- server02 was moved into the `Servers` OU.

---

## 15. What This Configuration Gave Me

By completing this stage, I had created the identity and management foundation for the rest of the Windows Server project.

The environment now provided:

- Centralised user management
- Computer account management
- Security group management
- Domain authentication
- Organisational Units for administration
- A Domain Controller
- DNS integration
- Domain-joined servers
- FSMO role awareness and verification

The structure also prepared the environment for the next stages of the project, where I used Active Directory objects and OUs for Group Policy, permissions, shared resources, and other Windows Server services.

---

## Key Skills Demonstrated

- Active Directory Domain Services
- Domain Controller administration
- Active Directory Users and Computers
- Organisational Unit management
- User account creation
- Security group creation
- Group membership management
- Computer account management
- Windows Server domain joining
- Domain authentication
- DNS-dependent domain connectivity testing
- FSMO role identification
- Active Directory organisation and administration

---

## Commands Used

### Test Domain Controller DNS Resolution

```cmd
ping server01.corp.danieltraining.com
```

### Identify FSMO Role Holders

```cmd
netdom query fsmo
```

---

## Key Takeaway

This was the point where the lab stopped being just a Windows Server installation and became an actual Windows domain environment.

I learned how users, groups, computers, DNS, and Domain Controllers fit together and how Active Directory provides the central structure used to manage them.

The most important lesson for me was that Active Directory is not just about creating user accounts. The way those users, computers, and groups are organised directly affects how policies, permissions, and security can be managed later.

---

## Next Step

The next stage of the project builds on this Active Directory structure by configuring **Group Policy Objects (GPOs)** and using the `End Users` OU to centrally apply user restrictions.

# Network Architecture

## Table of Contents

- [Overview](#overview)
- [Network Design](#network-design)
- [Network Topology](#network-topology)
- [IP Addressing](#ip-addressing)
- [Server01 — Domain Controller](#server01--domain-controller)
- [Server02 — Domain-Member Server](#server02--domain-member-server)
- [VMware Network Configuration](#vmware-network-configuration)
- [DNS Architecture](#dns-architecture)
- [DHCP Architecture](#dhcp-architecture)
- [Active Directory Domain](#active-directory-domain)
- [How the Services Work Together](#how-the-services-work-together)
- [Network Validation](#network-validation)
- [Important Configuration Notes](#important-configuration-notes)
- [Next Steps](#next-steps)

---

# Overview

The Windows Server 2025 homelab uses a local IPv4 network to allow the virtual servers, domain services, and client devices to communicate with one another.

I designed the network so that `server01` acts as the main infrastructure server. It provides Active Directory Domain Services, DNS, DHCP, and other services required by the lab.

A second Windows Server, `server02`, was added to the environment and joined to the Active Directory domain. This gave me another domain-joined server that could later be used for additional infrastructure roles such as file services.

The network was designed to keep the server addresses predictable while allowing client devices to receive their network configuration automatically through DHCP.

---

# Network Design

The lab uses the following core network:

| Component | Configuration |
|-----------|---------------|
| Network | `192.168.1.0/24` |
| Subnet Mask | `255.255.255.0` |
| Default Gateway | `192.168.1.1` |
| Domain Controller | `server01` |
| Server01 IP | `192.168.1.222` |
| Server02 | `server02` |
| Server02 IP | `192.168.1.223` |
| Active Directory Domain | `corp.danieltraining.com` |
| DHCP Range | `192.168.1.5 – 192.168.1.99` |
| VMware Network Mode | Bridged |

The `/24` subnet provides a single local network in which the servers, clients, and gateway can communicate.

---

# Network Topology

The basic network relationship can be represented as:

```text
                         Internet
                            │
                            │
                     ┌──────▼──────┐
                     │    Router   │
                     │ 192.168.1.1 │
                     └──────┬──────┘
                            │
                     Local Network
                    192.168.1.0/24
                            │
              ┌─────────────┴─────────────┐
              │                           │
       ┌──────▼──────┐             ┌──────▼──────┐
       │   server01  │             │   server02  │
       │192.168.1.222│             │192.168.1.223│
       │              │             │              │
       │ AD DS        │             │ Domain      │
       │ DNS          │             │ Member      │
       │ DHCP         │             │ Server      │
       └──────┬───────┘             └──────────────┘
              │
              │
       ┌──────▼──────┐
       │ Client      │
       │ Devices     │
       │ DHCP Range  │
       │ .5 – .99    │
       └─────────────┘
```

The servers were deployed as VMware virtual machines on my Windows 11 host computer.

---

# IP Addressing

I used static IP addresses for the servers because infrastructure services such as Active Directory and DNS need predictable addresses.

## Server01

`server01` was configured with:

| Setting | Value |
|---------|-------|
| Hostname | `server01` |
| IP Address | `192.168.1.222` |
| Subnet Mask | `255.255.255.0` |
| Default Gateway | `192.168.1.1` |
| Domain | `corp.danieltraining.com` |

During the initial configuration, I temporarily used public DNS servers while preparing the system. The preferred DNS server was initially configured as Cloudflare (`1.1.1.1`) with Google DNS (`8.8.8.8`) as the alternate server. After installing the DNS Server role, the server was configured to use its own DNS infrastructure.

## Server02

The second server was configured separately on the same network:

| Setting | Value |
|---------|-------|
| Hostname | `server02` |
| IP Address | `192.168.1.223` |
| DNS Server | `192.168.1.222` |
| Domain | `corp.danieltraining.com` |

Server02 was configured to use server01 as its preferred DNS server because Active Directory domain discovery depends heavily on DNS.

---

# Server01 — Domain Controller

Server01 became the primary infrastructure server in the lab.

Its main responsibilities include:

- Active Directory Domain Services
- DNS
- DHCP
- Domain authentication
- Group Policy management
- Network name resolution
- Infrastructure administration

The server was given the static address `192.168.1.222`.

I chose a static address because other systems need to consistently locate the Domain Controller and DNS server.

---

# Server02 — Domain-Member Server

I created a second Windows Server 2025 virtual machine named `server02`.

The purpose of adding a second server was to make the lab more representative of a real environment where different servers can perform different responsibilities.

Server02 was configured with:

```text
Hostname: server02
IP Address: 192.168.1.223
Preferred DNS: 192.168.1.222
Domain: corp.danieltraining.com
```

Before joining the domain, I verified DNS connectivity by running:

```powershell
ping server01.corp.danieltraining.com
```

The hostname successfully resolved to server01, confirming that server02 could locate the Domain Controller through DNS.

I then joined server02 to:

```text
corp.danieltraining.com
```

After restarting the server, I verified the new computer object in Active Directory Users and Computers and moved it into the `Servers` OU.

---

# VMware Network Configuration

The Windows Server virtual machine was configured in VMware Workstation using **Bridged networking**.

With Bridged mode enabled, the virtual machine appears as a separate device on the physical local network rather than being hidden behind VMware NAT.

This was important for my lab because I wanted the virtual servers to communicate with other devices on the local network and behave more like physical servers.

The main server01 virtual machine was configured with:

| VMware Component | Configuration |
|------------------|---------------|
| VM Name | `server01` |
| RAM | 8 GB |
| CPU | 4 cores |
| Virtual Disk | 60 GB |
| Network Adapter | Bridged |
| Guest OS | Windows Server 2025 |

I also created a VMware snapshot named **Before Install** before beginning the Windows Server installation. This gave me a clean recovery point if I needed to restart the deployment.

---

# DNS Architecture

DNS is one of the most important components of this environment because Active Directory relies heavily on name resolution.

The Active Directory domain is:

```text
corp.danieltraining.com
```

The primary DNS server is server01:

```text
192.168.1.222
```

I configured DNS records including:

| Record | Purpose |
|--------|---------|
| `server01` | Maps the Domain Controller hostname to its IP address |
| `router` | Maps the router hostname to `192.168.1.1` |
| `gateway` | CNAME alias pointing to `router` |

I also created a reverse lookup zone for the `192.168.1.0/24` network and configured PTR resolution.

For example:

```text
192.168.1.1
      ↓
router.corp.danieltraining.com
```

I tested forward DNS resolution using:

```powershell
ping router
```

and reverse DNS resolution using:

```powershell
nslookup 192.168.1.1
```

These tests confirmed that both forward and reverse name resolution were functioning correctly.

---

# DHCP Architecture

After configuring Active Directory and DNS, I installed the DHCP Server role on server01.

The DHCP scope was configured as:

| DHCP Setting | Value |
|--------------|-------|
| Scope Name | `LAN Network – Lab Environment` |
| Start Address | `192.168.1.5` |
| End Address | `192.168.1.99` |
| Subnet Mask | `255.255.255.0` |
| Default Gateway | `192.168.1.1` |
| DNS Server | `192.168.1.222` |
| Lease Duration | 8 days |

The DHCP range was intentionally separate from the static server addresses.

This meant server01 and server02 could retain predictable addresses while client devices received addresses automatically.

I tested DHCP using:

```powershell
ipconfig /release
ipconfig /renew
ipconfig /all
```

The client received an address within the configured `192.168.1.5 – 192.168.1.99` range, received `192.168.1.1` as its default gateway, and used server01 (`192.168.1.222`) for DNS.

---

# Active Directory Domain

The Active Directory environment uses:

```text
corp.danieltraining.com
```

Server01 operates as the Domain Controller.

The domain provides centralized management for:

- User accounts
- Computer accounts
- Security groups
- Organizational Units
- Authentication
- Group Policy
- Access control

I created a structured OU hierarchy including:

```text
corp.danieltraining.com
└── Daniel Training
    ├── End Users
    ├── Security Groups
    └── Servers
```

This structure allowed me to separate users, groups, and servers logically and provided a foundation for applying Group Policy later in the project.

---

# How the Services Work Together

One of the most important things I learned from building this environment was that Windows infrastructure services are closely connected.

The basic workflow is:

```text
Client Device
     │
     │ DHCP Request
     ▼
   DHCP
 server01
     │
     │ IP Address + Gateway + DNS
     ▼
Client receives network configuration
     │
     │ DNS Query
     ▼
   DNS Server
 server01
     │
     │ Resolves domain resources
     ▼
Active Directory
     │
     │ Authentication / Policies
     ▼
Domain Resources
```

DHCP provides the client with its network configuration.

DNS allows the client to locate servers and domain resources.

Active Directory provides centralized authentication and identity management.

Group Policy then allows configuration and security settings to be managed centrally.

This interaction between services is what makes the environment function as an integrated Windows domain rather than a collection of independent servers.

---

# Network Validation

I performed several tests throughout the deployment to verify that the network was functioning correctly.

## DNS Resolution

I tested hostname resolution with:

```powershell
ping server01.corp.danieltraining.com
```

The hostname successfully resolved to server01.

---

## Reverse DNS

I tested reverse name resolution using:

```powershell
nslookup 192.168.1.1
```

The address successfully resolved to:

```text
router.corp.danieltraining.com
```

---

## DHCP

I refreshed the client's DHCP lease using:

```powershell
ipconfig /release
ipconfig /renew
ipconfig /all
```

I verified that the client received an address from the correct DHCP range and received server01 as its DNS server.

---

## Domain Connectivity

I verified that server02 could locate the Domain Controller through DNS before attempting the domain join.

This helped prevent a common Active Directory configuration problem where a domain member is configured to use an external DNS server instead of the Domain Controller.

---

# Important Configuration Notes

## Servers Use Static Addresses

Server01 and server02 were configured with static IP addresses so that infrastructure services could reliably locate them.

## Domain Members Use Domain DNS

Server02 was configured to use:

```text
192.168.1.222
```

as its preferred DNS server.

This is important because Active Directory uses DNS to locate domain controllers and other domain services.

## DHCP Does Not Assign Server Addresses

The DHCP scope begins at:

```text
192.168.1.5
```

while the servers use:

```text
192.168.1.222
192.168.1.223
```

This keeps the server addresses outside the DHCP pool.

## Bridged VMware Networking

Using Bridged networking allows the virtual machines to participate directly in the local network, which made it easier to test communication between the servers and client devices.

---

# Next Steps

➡️ Continue to **05-Active-Directory.md**

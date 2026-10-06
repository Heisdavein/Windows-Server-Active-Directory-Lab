# DHCP Server Configuration

## Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Step 1 — Installing the DHCP Server Role](#step-1--installing-the-dhcp-server-role)
- [Step 2 — Creating the DHCP Scope](#step-2--creating-the-dhcp-scope)
- [Step 3 — Verifying DHCP Functionality](#step-3--verifying-dhcp-functionality)
- [Monitoring DHCP Leases](#monitoring-dhcp-leases)
- [DHCP Configuration Summary](#dhcp-configuration-summary)
- [How DHCP Works with Active Directory and DNS](#how-dhcp-works-with-active-directory-and-dns)
- [Validation](#validation)
- [Key Skills Demonstrated](#key-skills-demonstrated)

---

## Overview

After configuring Active Directory and DNS, the next service I configured was **Dynamic Host Configuration Protocol (DHCP)**.

DHCP automatically assigns IP addresses and other network settings to client devices when they connect to the network. Without DHCP, every computer, laptop, printer, or other network device would need to be configured with a static IP address manually.

For this lab, I installed the **DHCP Server role on server01** and configured it to distribute IP addresses to devices connected to my network.

The DHCP server was integrated with the existing Active Directory and DNS environment, allowing clients to automatically receive the correct gateway and DNS configuration.

---

## Prerequisites

Before installing DHCP, I made sure the following requirements had already been completed:

- `server01` had a static IP address.
- `server01` had already been promoted to a Domain Controller.
- DNS was already configured on `server01`.
- The Active Directory domain was already established.
- I was signed in using the Domain Administrator account.

The server's static IP address was:

```text
192.168.1.222
```

The lab domain was:

```text
corp.danieltraining.com
```

With these prerequisites in place, I was ready to install and configure DHCP.

---

# Step 1 — Installing the DHCP Server Role

I started by opening **Server Manager**.

1. I selected **Manage**.
2. I clicked **Add Roles and Features**.
3. I clicked **Next** through the initial pages of the wizard.
4. I left **Role-based or feature-based installation** selected.
5. I confirmed that `server01` was the destination server.
6. From the available server roles, I selected **DHCP Server**.
7. When Windows prompted me to install the required supporting features, I clicked **Add Features**.
8. I continued through the remaining pages while keeping the default settings.
9. On the Confirmation page, I enabled:

```text
Restart the destination server automatically if required
```

10. I clicked **Install** and waited for the DHCP Server role installation to complete.

### Completing DHCP Configuration

After the installation finished, I noticed a yellow notification flag in Server Manager.

I clicked the notification and selected **Complete DHCP Configuration**.

The post-installation wizard guided me through the remaining configuration.

After clicking **Next**, I selected **Commit**.

This authorised the DHCP server within Active Directory.

### Why DHCP Authorisation Matters

In an Active Directory environment, DHCP servers must be authorised before they can lease IP addresses.

This provides an important security control because it helps prevent unauthorised or rogue DHCP servers from distributing incorrect network settings to clients.

For example, an unauthorised DHCP server could potentially provide clients with the wrong gateway or DNS server, causing network connectivity or domain-resolution problems.

After the authorisation completed successfully, I closed the wizard.

---

# Step 2 Creating the DHCP Scope

With the DHCP role installed, I opened the DHCP management console.

1. I opened **Server Manager**.
2. I selected **Tools**.
3. I launched **DHCP**.
4. Inside DHCP Manager, I expanded `server01`.
5. I selected **IPv4**.
6. I right-clicked **IPv4** and selected **New Scope**.

A DHCP scope defines the range of IP addresses that DHCP is allowed to assign to client devices. It also contains network configuration such as the subnet mask, default gateway, DNS server, and lease duration.

Microsoft describes a DHCP scope as an administrative grouping of IP addresses that a DHCP server can lease to clients on a subnet.

### Scope Name

I gave the scope the following name:

```text
LAN Network – Lab Environment
```

### IP Address Range

I configured the DHCP address range as:

| Setting | Configuration |
|---|---|
| Start IP Address | `192.168.1.5` |
| End IP Address | `192.168.1.99` |
| Subnet Mask | `255.255.255.0` |
| CIDR | `/24` |

This gave DHCP a defined pool of addresses that it could dynamically assign to clients.

### DHCP Exclusions

The wizard then asked me to configure exclusions.

Exclusions are addresses inside the DHCP scope that should never be leased to clients.

In this lab, I could have excluded addresses such as:

```text
192.168.1.1 – 192.168.1.4
```

These addresses could commonly be used for routers, servers, or other infrastructure.

However, I did **not** create an exclusion range because my static devices were already using addresses outside the DHCP range.

I therefore left the exclusion list unchanged and continued.

### Lease Duration

I kept the default DHCP lease duration of:

```text
8 days
```

The lease duration determines how long a client can keep its dynamically assigned IP address before it needs to renew the lease.

### DHCP Options

When the wizard asked whether I wanted to configure DHCP options immediately, I selected:

```text
Yes
```

I then configured the default gateway.

### Default Gateway

I entered my router's IP address:

```text
192.168.1.1
```

I clicked **Add** and continued.

### DNS Configuration

The wizard automatically detected the Active Directory domain and DNS configuration.

I confirmed the domain information and verified that the DNS server pointed to:

```text
192.168.1.222
```

This is the IP address of `server01`, which was also providing DNS services.

Using the domain controller as the DNS server was important because domain-joined clients need to be able to resolve internal Active Directory resources.

### WINS

The wizard then displayed the WINS configuration page.

I was not using **Windows Internet Name Service (WINS)** in this environment, so I skipped this section.

### Activating the Scope

Finally, I selected:

```text
Activate this scope now
```

I completed the wizard by clicking **Finish**.

At this point, the DHCP server was fully configured and ready to begin assigning IP addresses to client devices.

---

# Step 3 Verifying DHCP Functionality

After configuring DHCP, I wanted to confirm that clients were actually receiving the correct network configuration.

Rather than waiting for the client to request a new address automatically, I forced it to obtain a fresh DHCP lease.

I opened Command Prompt and ran:

```cmd
ipconfig /release
ipconfig /renew
ipconfig /all
```

### What Each Command Does

#### `ipconfig /release`

```cmd
ipconfig /release
```

This releases the client's existing DHCP-assigned IP address.

#### `ipconfig /renew`

```cmd
ipconfig /renew
```

This requests a new IP address lease from the DHCP server.

#### `ipconfig /all`

```cmd
ipconfig /all
```

This displays the complete network configuration, allowing me to verify the address, gateway, DNS server, and other network information.

### Verification Results

After running the commands, I confirmed that the client had received an IP address within my configured DHCP range:

```text
192.168.1.5 – 192.168.1.99
```

I also confirmed:

- **Default Gateway:** `192.168.1.1`
- **DNS Server:** `192.168.1.222`
- The client received its network configuration through DHCP.

This confirmed that DHCP was functioning correctly.

---

# Monitoring DHCP Leases

One feature I found particularly useful was the ability to monitor DHCP activity directly through **DHCP Manager**.

After expanding the DHCP scope, I could view several important sections.

### Address Pool

The **Address Pool** displays the range of IP addresses available for DHCP assignment.

For this lab, the configured pool was:

```text
192.168.1.5 – 192.168.1.99
```

### Address Leases

The **Address Leases** section shows devices that have received IP addresses from the DHCP server.

This allows an administrator to see information such as:

- Hostname
- Assigned IP address
- Lease information
- Lease expiration

This is useful when troubleshooting network connectivity or identifying which device currently holds a particular DHCP address.

### Reservations

The **Reservations** section allows an administrator to permanently associate a particular IP address with a device's MAC address.

This is useful when a device needs to receive the same IP address every time it connects to the network.

Examples include:

- Printers
- Network switches
- Wireless access points
- Other infrastructure devices

Reservations provide the convenience of DHCP while allowing important devices to consistently receive the same address.

---

# DHCP Configuration Summary

The final DHCP configuration for my lab was:

| Configuration | Value |
|---|---|
| DHCP Server | `server01` |
| Server IP | `192.168.1.222` |
| Scope Name | `LAN Network – Lab Environment` |
| Network | `192.168.1.0/24` |
| Subnet Mask | `255.255.255.0` |
| Start IP | `192.168.1.5` |
| End IP | `192.168.1.99` |
| Default Gateway | `192.168.1.1` |
| DNS Server | `192.168.1.222` |
| Lease Duration | `8 days` |
| WINS | Not configured |
| Exclusions | None |
| Scope Status | Active |

---

# How DHCP Works with Active Directory and DNS

One of the most useful parts of this stage was seeing how DHCP, DNS, and Active Directory work together rather than treating them as completely separate technologies.

The basic flow in my lab was:

```text
Client Device
     │
     │ DHCP Request
     ▼
server01
DHCP Server
     │
     ├── IP Address → 192.168.1.x
     ├── Gateway → 192.168.1.1
     └── DNS → 192.168.1.222
                    │
                    ▼
             DNS / Active Directory
             corp.danieltraining.com
```

DHCP provides the client with the network configuration it needs.

DNS allows the client to resolve names and locate resources within the domain.

Active Directory provides centralised identity and management for the Windows environment.

Seeing these services work together gave me a much better understanding of how Windows-based enterprise networks are structured.

---

# Validation

I used the following checks to validate the DHCP configuration:

### 1. DHCP Role Installation

Confirmed that the **DHCP Server** role was installed successfully on `server01`.

### 2. DHCP Authorisation

Confirmed that the DHCP server was authorised through Active Directory.

### 3. Scope Configuration

Confirmed that the active scope contained:

```text
192.168.1.5 – 192.168.1.99
```

### 4. Client Lease

Used:

```cmd
ipconfig /release
ipconfig /renew
```

to force the client to request a new DHCP lease.

### 5. Network Configuration

Used:

```cmd
ipconfig /all
```

to verify the received configuration.

### 6. Gateway Verification

Confirmed:

```text
192.168.1.1
```

was provided as the default gateway.

### 7. DNS Verification

Confirmed:

```text
192.168.1.222
```

was provided as the DNS server.

### 8. DHCP Manager

Verified the DHCP scope, address pool, leases, and reservation sections through DHCP Manager.

---

# Screenshots

The following screenshots should be included in the repository where available.

> 📸 **Screenshot Placeholder**  
> **Filename:** `07-01-dhcp-server-role.png`  
> **Description:** Server Manager showing the DHCP Server role installed on `server01`.

> 📸 **Screenshot Placeholder**  
> **Filename:** `07-02-dhcp-scope-wizard.png`  
> **Description:** DHCP New Scope Wizard showing the `LAN Network – Lab Environment` scope configuration.

> 📸 **Screenshot Placeholder**  
> **Filename:** `07-03-dhcp-address-pool.png`  
> **Description:** DHCP Manager showing the configured address pool from `192.168.1.5` to `192.168.1.99`.

> 📸 **Screenshot Placeholder**  
> **Filename:** `07-04-dhcp-address-leases.png`  
> **Description:** DHCP Manager showing a client lease assigned from the configured DHCP scope.

> 📸 **Screenshot Placeholder**  
> **Filename:** `07-05-ipconfig-verification.png`  
> **Description:** Command Prompt showing `ipconfig /release`, `ipconfig /renew`, and `ipconfig /all` verification.

---

# Key Skills Demonstrated

This section demonstrates practical experience with:

- Windows Server 2025
- DHCP Server installation
- DHCP scope creation
- IPv4 address management
- DHCP lease management
- DHCP scope options
- Default gateway configuration
- DNS server configuration
- DHCP authorisation in Active Directory
- DHCP troubleshooting
- `ipconfig` diagnostics
- Address pool monitoring
- DHCP lease monitoring
- DHCP reservations
- Integration between DHCP, DNS, and Active Directory

---

## What I Learned

This stage helped me understand that DHCP is more than simply assigning IP addresses.

A correctly configured DHCP server automatically provides clients with the information they need to communicate on the network, including their IP address, subnet mask, default gateway, and DNS server.

The most important lesson for me was seeing how DHCP fits into the wider Windows Server environment. `server01` was not operating as an isolated DHCP server. It was already providing Active Directory and DNS services, allowing DHCP to provide clients with the correct information required to operate within the `corp.danieltraining.com` domain.

Completing this section gave me practical experience installing, authorising, configuring, testing, and monitoring DHCP in a Windows Server environment.

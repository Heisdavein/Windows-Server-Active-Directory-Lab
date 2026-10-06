# DNS Configuration

## Overview

After configuring Active Directory, I moved on to DNS.

DNS was an important part of the lab because Active Directory relies heavily on name resolution. Instead of having to remember IP addresses for every system, DNS allows devices and services to be located using names.

For this lab, `server01` provided DNS services for the domain:

```text
corp.danieltraining.com
```

I also created custom DNS records for my network gateway and configured reverse DNS so that an IP address could be resolved back to a hostname.

---

## DNS Environment

| Component | Configuration |
|---|---|
| DNS Server | `server01` |
| DNS Server IP | `192.168.1.222` |
| Domain | `corp.danieltraining.com` |
| Network | `192.168.1.0/24` |
| Default Gateway | `192.168.1.1` |
| Router Hostname | `router` |
| Gateway Alias | `gateway` |

---

## 1. Opening DNS Manager

I opened DNS Manager through:

```text
Server Manager
→ Tools
→ DNS
```

From the navigation pane, I expanded the server name and then opened:

```text
Forward Lookup Zones
```

I selected my domain's forward lookup zone:

```text
corp.danieltraining.com
```

Inside the zone, I could already see DNS records that Windows and Active Directory had created automatically.

One of these was an **A (Host)** record for `server01`, which mapped the server's hostname to its IP address.

This helped demonstrate how closely Active Directory and DNS work together.

---

## 2. Creating an A Record

The first DNS record I manually created was an **A (Host)** record.

An A record maps a hostname to an IPv4 address.

### Record Configuration

I right-clicked inside the forward lookup zone and selected:

```text
New Host (A or AAAA)
```

I entered:

```text
Name: router
IP Address: 192.168.1.1
```

I left:

```text
Create associated pointer (PTR) record
```

unchecked for this initial record and clicked **Add Host**.

The resulting record was:

```text
router → 192.168.1.1
```

This gave my network gateway a DNS hostname within the domain.

---

## 3. Testing the A Record

After creating the record, I wanted to confirm that DNS resolution was working.

I opened Command Prompt and ran:

```cmd
ping router
```

The hostname successfully resolved to:

```text
router.corp.danieltraining.com
```

and then to:

```text
192.168.1.1
```

The successful replies confirmed that the A record was functioning correctly.

---

## 4. Creating a CNAME Record

Next, I created a **CNAME (Canonical Name)** record.

Unlike an A record, which points directly to an IP address, a CNAME creates an alias for another hostname.

I right-clicked inside the forward lookup zone and selected:

```text
New Alias (CNAME)
```

I configured the alias as:

```text
Alias name:
gateway
```

For the target hostname, I entered:

```text
router.corp.danieltraining.com
```

The resulting relationship was:

```text
gateway
   ↓
router.corp.danieltraining.com
   ↓
192.168.1.1
```

This meant `gateway` could be used as another name for the `router` host without creating another A record.

---

## 5. Why the CNAME Was Useful

Creating the CNAME helped me understand the difference between directly assigning an IP address and creating an alias.

The A record was responsible for:

```text
router → 192.168.1.1
```

The CNAME was responsible for:

```text
gateway → router.corp.danieltraining.com
```

This allows multiple hostnames to reference the same underlying system without having to duplicate the IP address in multiple A records.

---

## 6. Creating a Reverse Lookup Zone

After configuring forward DNS resolution, I also wanted to configure reverse DNS.

Forward DNS answers:

```text
Hostname → IP Address
```

Reverse DNS performs the opposite lookup:

```text
IP Address → Hostname
```

For my lab network, I created a reverse lookup zone for:

```text
192.168.1.0/24
```

This allowed DNS to associate IP addresses on my local network with their corresponding hostnames.

---

## 7. Configuring the Reverse Lookup Zone

In DNS Manager, I opened:

```text
Reverse Lookup Zones
```

I then created a new reverse lookup zone for the network:

```text
192.168.1.0/24
```

The network information was based on:

```text
Network ID: 192.168.1.0
Subnet Mask: 255.255.255.0
```

The purpose of this zone was to support PTR records for reverse name resolution.

---

## 8. PTR Resolution

The reverse DNS configuration allowed an IP address such as:

```text
192.168.1.1
```

to resolve back to the hostname:

```text
router.corp.danieltraining.com
```

This is useful during troubleshooting because sometimes I may have an IP address but not know which device it belongs to.

Instead of only asking:

```text
What IP address does this hostname use?
```

I could also ask:

```text
What hostname belongs to this IP address?
```

---

## 9. Testing Reverse DNS

To test the reverse lookup configuration, I opened Command Prompt and ran:

```cmd
nslookup 192.168.1.1
```

The expected reverse lookup result was:

```text
router.corp.danieltraining.com
```

This confirmed that the IP address could be resolved back to the hostname through the reverse lookup configuration.

---

## 10. DNS Records Created

The main DNS records configured during this section were:

| Record Type | Name | Target / Address |
|---|---|---|
| A | `server01` | `192.168.1.222` |
| A | `router` | `192.168.1.1` |
| CNAME | `gateway` | `router.corp.danieltraining.com` |
| PTR | `192.168.1.1` | `router.corp.danieltraining.com` |

The `server01` A record was automatically present as part of the Active Directory/DNS configuration, while `router` and `gateway` were manually created during this exercise.

---

## 11. DNS Verification

I used several tests to confirm that the DNS configuration was working.

### Forward Lookup

```cmd
ping router
```

Expected behaviour:

```text
router
↓
router.corp.danieltraining.com
↓
192.168.1.1
```

### Reverse Lookup

```cmd
nslookup 192.168.1.1
```

Expected result:

```text
router.corp.danieltraining.com
```

### Domain Controller Resolution

I also verified that the second server could locate the Domain Controller using its fully qualified domain name:

```cmd
ping server01.corp.danieltraining.com
```

The hostname resolved to:

```text
192.168.1.222
```

This was particularly important because server02 needed to locate `server01` before joining the Active Directory domain.

---

## 12. DNS and Active Directory

One of the most important lessons I took from this section was how closely DNS and Active Directory are connected.

Active Directory automatically creates and uses DNS records to help domain members locate important services.

For example:

```text
server02
   ↓
DNS
   ↓
server01.corp.danieltraining.com
   ↓
192.168.1.222
   ↓
Domain Controller
```

This is why I configured server02 to use `192.168.1.222` as its preferred DNS server before attempting the domain join.

Using public DNS servers such as:

```text
1.1.1.1
8.8.8.8
```

would not provide the internal Active Directory DNS records required to locate the Domain Controller.

---

## 13. Troubleshooting Lesson

The DNS configuration also showed me why DNS should be one of the first things I check when troubleshooting an Active Directory environment.

If a domain-joined computer cannot resolve the Domain Controller's hostname, several things can fail:

- Domain joins
- Authentication
- Access to domain resources
- Group Policy processing
- Communication with domain services

For that reason, testing DNS resolution with tools such as:

```cmd
ping
nslookup
```

is a useful first step when investigating connectivity or Active Directory problems.

---

## Key Skills Demonstrated

- Windows DNS Server administration
- DNS Manager
- Forward lookup zones
- Reverse lookup zones
- A records
- CNAME records
- PTR records
- Hostname-to-IP resolution
- IP-to-hostname resolution
- DNS troubleshooting
- Active Directory DNS integration
- `ping`
- `nslookup`

---

## Commands Used

### Test Forward DNS Resolution

```cmd
ping router
```

### Test Domain Controller Resolution

```cmd
ping server01.corp.danieltraining.com
```

### Test Reverse DNS Resolution

```cmd
nslookup 192.168.1.1
```

---

## Key Takeaway

This section gave me practical experience with both forward and reverse DNS resolution.

I learned how to create an A record, create a CNAME alias, configure a reverse lookup zone, and verify DNS resolution from the command line.

More importantly, I gained a clearer understanding of why DNS is so important to Active Directory. It is not simply a service that translates names into IP addresses; it is a fundamental part of how Windows domain computers locate and communicate with the services they depend on.

---

## Next Step

The next stage of the project is **DHCP**, where I configured `server01` to automatically provide IP addresses and other network settings to devices on the network.

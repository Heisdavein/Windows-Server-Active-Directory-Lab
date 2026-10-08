# Windows Defender Firewall – Creating and Managing Firewall Rules

## Table of Contents

- [Overview](#overview)
- [Opening Windows Defender Firewall with Advanced Security](#opening-windows-defender-firewall-with-advanced-security)
- [Understanding Inbound and Outbound Rules](#understanding-inbound-and-outbound-rules)
  - [Inbound Rules](#inbound-rules)
  - [Outbound Rules](#outbound-rules)
- [Reviewing the Existing Firewall Rules](#reviewing-the-existing-firewall-rules)
- [Creating a Custom Inbound Rule](#creating-a-custom-inbound-rule)
  - [Step 1 — Open Inbound Rules](#step-1--open-inbound-rules)
  - [Step 2 — Create a New Rule](#step-2--create-a-new-rule)
  - [Step 3 — Select Port](#step-3--select-port)
  - [Step 4 — Configure TCP Port 8080](#step-4--configure-tcp-port-8080)
  - [Step 5 — Allow the Connection](#step-5--allow-the-connection)
  - [Step 6 — Select Network Profiles](#step-6--select-network-profiles)
  - [Step 7 — Name the Rule](#step-7--name-the-rule)
- [Verifying the New Rule](#verifying-the-new-rule)
- [Key Security Principle I Learned](#key-security-principle-i-learned)
- [Screenshots](#screenshots)
- [What I Learned](#what-i-learned)

---

## Overview

As I continued building my Windows Server lab, I wanted to understand how Windows controls network traffic entering and leaving the server.

This is where **Windows Defender Firewall with Advanced Security** plays an important role.

Every connection made to or from a Windows Server is evaluated by firewall rules before the traffic is allowed to proceed. These rules determine whether specific network communication should be permitted or blocked.

While working through this section, I learned that firewall configuration requires a balance.

If the firewall is too restrictive, legitimate users and services may not be able to communicate with the server. If unnecessary traffic is allowed, the server can be exposed to additional security risks.

The goal is therefore to allow only the connections that are genuinely required.

---

## Opening Windows Defender Firewall with Advanced Security

To begin exploring the firewall, I opened the advanced management console.

### Step 1 — Search for the Firewall Console

I opened the **Start** menu and searched for:

```text
Windows Defender Firewall with Advanced Security
```

I then launched the application.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/8837c03a-afd8-40af-ac22-7f2d19909f1f" />
Windows Defender Firewall with Advanced Security console showing the Inbound Rules and Outbound Rules sections.

The advanced console gave me much more detailed control than the standard Windows Firewall interface.

From here, I could:

- View existing firewall rules.
- Create new rules.
- Modify existing rules.
- Review inbound and outbound traffic rules.
- Monitor firewall configuration.

---

## Understanding Inbound and Outbound Rules

As I explored the console, I noticed that firewall rules are divided into two main categories:

- **Inbound Rules**
- **Outbound Rules**

Understanding the difference between these two is important when troubleshooting connectivity and securing a Windows Server.

---

### Inbound Rules

**Inbound rules** determine what network traffic is allowed to reach the server.

For example, when another computer attempts to connect to my server through:

- Remote Desktop
- DNS
- DHCP
- A web application

the firewall checks the relevant inbound rules to determine whether the connection should be permitted.

These are generally some of the rules administrators spend significant time managing because they directly control what can access services running on the server.

---

### Outbound Rules

**Outbound rules** control network traffic leaving the server.

By default, Windows allows most outbound traffic, which is sufficient for many environments.

However, in highly secure organisations, outbound rules can be used to restrict which applications or services are allowed to communicate with external systems.

This can help reduce the risk of:

- Malware communicating with external systems.
- Unauthorised data transfers.
- Applications communicating with systems they do not need to access.

> **Key distinction:** Inbound rules control traffic coming **into** the server, while outbound rules control traffic going **out of** the server.

---

## Reviewing the Existing Firewall Rules

Before creating any new rules, I spent time reviewing the firewall configuration Windows had already created automatically.

I selected:

```text
Inbound Rules
```

I found that numerous rules were already present.

This made sense because earlier in the project I had installed several Windows Server roles, including:

- Active Directory Domain Services (AD DS)
- DNS Server
- DHCP Server

During installation, Windows automatically created the firewall rules required for these services to function correctly.

### Examples of Automatically Created Rules

#### DHCP Server

The DHCP Server automatically creates rules allowing communication over:

```text
UDP 67
UDP 68
```

#### DNS Server

The DNS Server creates rules allowing both:

```text
TCP 53
UDP 53
```

#### Active Directory Domain Services

Active Directory Domain Services creates a larger collection of firewall rules to support:

- Authentication
- Directory services
- Replication
- Communication between domain controllers
- Communication between domain controllers and client computers

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/eae2bf98-28c3-493a-a41f-eb235fce1a4e" />
Inbound Rules showing existing Active Directory Domain Services, DHCP Server, DNS Server, File and Printer Sharing, Remote Desktop, and Windows Management Instrumentation rules.

I found this particularly useful because it demonstrated how Windows Server simplifies administration by automatically configuring many essential firewall settings when server roles are installed.

Even though I did not need to create these rules manually, reviewing them helped me understand which services required which network ports.

---

# Creating a Custom Inbound Rule

To gain hands-on experience with firewall management, I created my own inbound rule.

For this example, I imagined that I had deployed a web application running on:

```text
TCP port 8080
```

Because Windows Firewall blocks unsolicited traffic by default, I needed to create a rule that would allow incoming connections on that port.

The final rule was:

| Setting | Configuration |
|---|---|
| Rule type | Port |
| Protocol | TCP |
| Local port | 8080 |
| Action | Allow the connection |
| Profile | Domain, Private, Public |
| Rule name | `Allow TCP 8080 - Web App` |
| Description | `Allows incoming connections to the web application on TCP port 8080.` |

---

## Step 1- Open Inbound Rules

Inside **Windows Defender Firewall with Advanced Security**, I selected:

```text
Inbound Rules
```

I then right-clicked **Inbound Rules** and selected:

```text
New Rule
```
<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/3f6af926-447f-4e21-912c-f7c7aafca8b2" />
 New Inbound Rule Wizard showing the Rule Type page with Port selected.

---

## Step 2- Create a New Rule

The **New Inbound Rule Wizard** opened.

The available rule types included:

- Program
- Port
- Predefined
- Custom

I selected:

```text
Port
```

The Port option creates a firewall rule that controls connections for a TCP or UDP port.

I then clicked:

```text
Next
```

---

## Step 3- Select Port

On the **Protocol and Ports** page, I selected:

```text
TCP
```

This was important because the web application example was intended to communicate using TCP.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/91c0fb08-62c1-4f12-a330-369d5bff4c53" />
 Protocol and Ports page showing TCP selected and port 8080 entered as the specific local port.

---

## Step 4 — Configure TCP Port 8080

Under **Specific local ports**, I entered:

```text
8080
```

The configuration was therefore:

```text
Protocol type: TCP
Specific local ports: 8080
```

I then continued to the next stage of the wizard.

---

## Step 5 — Allow the Connection

The wizard then asked what action should be taken when a connection matched the conditions of the rule.

The available options included:

- Allow the connection
- Allow the connection if it is secure
- Block the connection

I selected:

```text
Allow the connection
```

I then clicked **Next**.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/b89b6740-5363-450c-903a-b5ffda9ebf00" />
 Action page of the New Inbound Rule Wizard with Allow the connection selected.

---

## Step 6 — Select Network Profiles

The wizard then asked which network profiles the rule should apply to.

I selected all three available profiles:

```text
Domain
Private
Public
```

I then clicked **Next**.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/0871ed31-4df5-48d0-b18a-9304b05cd020" />
 Profile page showing Domain, Private, and Public selected.

---

## Step 7 — Name the Rule

On the final page, I entered the following rule name:

```text
Allow TCP 8080 - Web App
```

I also entered the description:

```text
Allows incoming connections to the web application on TCP port 8080.
```

I then clicked **Finish**.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/3b53cd4b-40f3-441d-8788-b49cff15695d" />
 Final page of the New Inbound Rule Wizard showing the exact rule name and description.

---

# Verifying the New Rule

After completing the wizard, I returned to **Inbound Rules**.

The new rule appeared in the list:

```text
Allow TCP 8080 - Web App
```

The rule was active immediately.

It allowed incoming connections to TCP port 8080, provided that a service was actually listening on that port.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/8a15364a-cfe9-4785-bb22-36417bb6de7b" />
Inbound Rules showing Allow TCP 8080 - Web App listed alongside the existing Windows Server firewall rules.

I also opened the rule's properties to review the configuration.

The rule showed:

```text
Name:
Allow TCP 8080 - Web App
```

and:

```text
Description:
Allows incoming connections to the web application on TCP port 8080.
```

The rule was enabled.

Under the protocol and port settings, the configuration showed:

```text
Protocol type: TCP
Local port: 8080
```

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/fea78cc5-e321-40f9-bbbb-cf54093d0c5c" />
Properties of Allow TCP 8080 - Web App showing the rule name, enabled status, TCP protocol, and local port 8080.

---

## Key Security Principle I Learned

While creating firewall rules, I was reminded of an important cybersecurity principle:

> **Only open the ports that are absolutely necessary.**

Every open network port represents another potential entry point into a system.

If a service is no longer required, its associated firewall rule should be reviewed and, where appropriate, disabled or removed.

This follows the **Principle of Least Privilege**, which means systems should only be given the minimum level of access required to perform their intended function.

The same philosophy applies to firewall configuration:

```text
Deny unnecessary traffic
        ↓
Allow only required communication
        ↓
Review rules regularly
        ↓
Remove rules that are no longer needed
```

In my lab, TCP port 8080 was opened specifically for the web application example. I would not open additional ports simply because they were available.

---

## What I Learned

This section gave me practical experience with one of the most important security features built into Windows Server.

I learned:

- How Windows Defender Firewall controls network communication.
- The difference between inbound and outbound rules.
- How Windows automatically creates firewall rules for installed server roles.
- How AD DS, DNS, and DHCP rely on firewall rules for their network communication.
- How to create a custom inbound firewall rule.
- How to configure a TCP port.
- How to allow a connection.
- How to apply a rule to Domain, Private, and Public profiles.
- How to review and verify a newly created rule.
- Why unnecessary open ports increase security risk.
- How the Principle of Least Privilege applies to firewall configuration.

The most important lesson for me was that firewall administration is not simply about blocking everything.

A useful firewall configuration allows legitimate services to work while restricting unnecessary network access.

By creating and reviewing the TCP 8080 rule myself, I gained a better understanding of how Windows Server makes those decisions and how administrators can control them.

---

## Screenshots

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/0b440d9a-74f6-4047-9321-db312913639b" />
Windows Defender Firewall with Advanced Security console showing the firewall management interface.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/91f7ed72-9100-4f38-b8b5-d701573431f6" />
Inbound Rules section displaying existing firewall rules for services such as AD DS, DNS, DHCP, and other Windows services.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/1457977d-b30f-4b20-b807-b48b9306edd6" />
New Inbound Rule Wizard showing the Rule Type selection step.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/e4b0a567-a6c5-4c98-9a2e-7537e1a1aa72" />
New Inbound Rule Wizard showing the Allow the connection action selected.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/c6506f0b-839c-4c04-9533-91857235c087" />
New Inbound Rule Wizard showing firewall rule scope selection for Domain, Private, and Public network profiles.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/bfcb097d-d517-402a-a073-78678fd51d35" />
Windows Defender Firewall with Advanced Security showing the newly created TCP 8080 inbound rule in the Inbound Rules list.
Firewall Rule Properties window showing the configuration details of the TCP 8080 inbound rule.




New Inbound Rule Wizard showing TCP selected with port 8080 configured as the specific local port.





---

## Skills Demonstrated

- Windows Defender Firewall administration
- Windows Server security
- Inbound firewall rule management
- Outbound firewall rule understanding
- TCP/IP and port awareness
- Windows Server role firewall dependencies
- Active Directory network security
- DNS firewall requirements
- DHCP firewall requirements
- Custom firewall rule creation
- Network profile configuration
- Principle of Least Privilege
- Security-focused troubleshooting
- Firewall configuration verification

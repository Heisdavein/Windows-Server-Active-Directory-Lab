# Group Policy Objects (GPOs)

## Table of Contents

- [Overview](#overview)
- [Understanding Group Policy](#understanding-group-policy)
- [How Group Policy Is Applied](#how-group-policy-is-applied)
- [Step 1 — Opening Group Policy Management](#step-1--opening-group-policy-management)
- [Exploring the Default Domain Policy](#exploring-the-default-domain-policy)
- [Step 2 — Creating a Custom Group Policy Object](#step-2--creating-a-custom-group-policy-object)
- [Step 3 — Forcing an Immediate Group Policy Update](#step-3--forcing-an-immediate-group-policy-update)
- [Step 4 — Checking the FSMO Roles](#step-4--checking-the-fsmo-roles)
- [Validation](#validation)
- [Screenshots](#screenshots)
- [Key Skills Demonstrated](#key-skills-demonstrated)
- [What I Learned](#what-i-learned)

---

# Overview

As I continued building my Windows Server environment, I reached one of the features that really demonstrates the power of Active Directory: **Group Policy**.

Group Policy allows administrators to configure settings centrally and apply them to users and computers throughout an organisation.

Instead of configuring every computer individually, I can create a policy once, link it to the appropriate location in Active Directory, and allow Windows to apply the settings automatically.

For this lab, I explored the existing **Default Domain Policy** and then created my own custom Group Policy Object (GPO) to restrict access to the Control Panel and PC Settings for users in the `End Users` organisational unit.

---

# Understanding Group Policy

Group Policy provides centralised management of Windows settings.

This becomes particularly useful as an organisation grows.

For example, imagine an organisation with **800 employee computers** that all need a PDF reader installed. Without centralised management, an administrator could potentially have to visit each computer individually or create a separate deployment process.

With Group Policy, the administrator can configure the software deployment once and link the policy to the appropriate Organisational Unit (OU).

When the computers refresh their policies, the configuration can be applied automatically.

This is one of the reasons Group Policy is such an important administration tool in Windows environments.

---

# How Group Policy Is Applied

One of the first concepts I learned was that Group Policy follows a specific processing hierarchy known as **LSDOU**:

```text
Local
  ↓
Site
  ↓
Domain
  ↓
Organisational Unit (OU)
```

### Local

Policies configured directly on an individual computer.

### Site

Policies applied at an Active Directory site, usually representing a geographical location or network boundary.

### Domain

Policies that affect users and computers throughout the domain.

### Organisational Unit

Policies targeted at specific users or computers within a particular OU.

Understanding this order helped me understand why policies can sometimes override one another.

If two policies conflict, the policy processed later generally takes precedence.

This also means that a policy linked to an OU can override a setting applied at an earlier level.

Administrators can also **Enforce** a Group Policy Object when a policy must remain in effect and lower-level OUs should not override it.

---

# Step 1 — Opening Group Policy Management

I began by opening **Group Policy Management**.

1. I opened **Server Manager**.
2. I selected **Tools**.
3. I clicked **Group Policy Management**.
4. Inside the console, I expanded my forest.
5. I expanded the domain:

```text
corp.danieltraining.com
```

I noticed that Windows had already created and linked a **Default Domain Policy** at the domain level.

Before creating my own policy, I opened the Default Domain Policy and explored its **Settings** tab to understand what Windows had configured by default.

---

# Exploring the Default Domain Policy

The Default Domain Policy provides a baseline level of security for computers and users within the domain.

While reviewing it, I identified several settings that were already configured.

### Password Expiration

Passwords were configured to expire after:

```text
42 days
```

### Minimum Password Length

The minimum password length was:

```text
7 characters
```

### Password Complexity

Password complexity was enabled.

This requires passwords to use a combination of character types such as:

- Uppercase letters
- Lowercase letters
- Numbers
- Special characters

### Account Lockout

The policy also included account lockout settings designed to help protect against repeated failed sign-in attempts.

For my lab, I left these default settings unchanged.

However, I also recognised that production environments would typically use stronger security requirements. For example, an organisation might increase the minimum password length to at least 12 characters and apply stricter authentication controls.

The important lesson for me was that the **Default Domain Policy already provides a security baseline**, but administrators should review those settings against the organisation's actual security requirements.

---

# Step 2 — Creating a Custom Group Policy Object

After exploring the default policy, I wanted to create my own GPO and see how a policy is created, linked, configured, and tested.

For this exercise, I created a policy that prevents end users from accessing the **Control Panel and PC Settings**.

The purpose was to prevent standard users from making unauthorised system changes.

## Creating the GPO

I right-clicked the:

```text
End Users
```

Organisational Unit and selected:

```text
Create a GPO in this domain, and Link it here
```

I named the policy:

```text
Disable Control Panel - End Users
```

I then clicked **OK**.

The policy was now linked directly to the `End Users` OU.

---

## Editing the GPO

After creating the policy, I right-clicked it and selected **Edit**.

This opened the **Group Policy Management Editor**.

I navigated to:

```text
User Configuration
    → Policies
        → Administrative Templates
            → Control Panel
```

Inside the Control Panel settings, I opened:

```text
Prohibit access to Control Panel and PC Settings
```

I changed the policy setting to:

```text
Enabled
```

Before applying the setting, I read Microsoft's explanation of what enabling the policy would do.

I then clicked:

```text
Apply
```

followed by:

```text
OK
```

The GPO was now configured.

---

# What the Policy Does

The policy is linked to the `End Users` OU.

Therefore, users located within that OU receive the policy when Group Policy is processed.

Once the policy has been applied, those users are prevented from accessing:

- Control Panel
- PC Settings

This demonstrated an important advantage of Group Policy.

Rather than configuring the restriction separately on every computer, I configured it once and linked it to the appropriate Active Directory OU.

---

# Step 3 — Forcing an Immediate Group Policy Update

Normally, Windows refreshes Group Policy automatically.

However, while testing a newly created policy, I did not want to wait for the normal refresh cycle.

I therefore opened **Command Prompt** on a domain-joined computer and ran:

```cmd
gpupdate /force
```

This command forces Windows to immediately retrieve and apply the latest Group Policy settings.

It is especially useful when troubleshooting because it allows an administrator to test policy changes without waiting for the normal refresh process.

After running the command, I could test whether the new restriction had been applied to the user.

---

# Step 4 — Checking the FSMO Roles

As I learned more about Active Directory, I also learned about the five specialised **Flexible Single Master Operations (FSMO)** roles.

These roles perform important functions within Active Directory, including responsibilities related to:

- Schema updates
- Relative Identifier (RID) allocation
- Domain operations
- Infrastructure operations
- Domain naming

Although an Active Directory environment can contain multiple Domain Controllers, each FSMO role can only be held by one Domain Controller at a time.

To identify which server owned the FSMO roles in my environment, I ran:

```cmd
netdom query fsmo
```

Because my lab contained only one Domain Controller, all five FSMO roles were assigned to:

```text
server01
```

This made sense for my lab because `server01` was the only Domain Controller.

In a larger production environment, FSMO roles may be distributed across multiple Domain Controllers to improve resilience and simplify maintenance.

---

# Validation

I used several checks to confirm that the Group Policy configuration was working.

### 1. GPO Creation

Confirmed that the custom GPO existed:

```text
Disable Control Panel - End Users
```

### 2. GPO Link

Confirmed that the GPO was linked to:

```text
Daniel Training
└── End Users
```

### 3. Policy Configuration

Confirmed that the following policy was enabled:

```text
Prohibit access to Control Panel and PC Settings
```

### 4. Policy Location

Confirmed the policy was configured under:

```text
User Configuration
→ Policies
→ Administrative Templates
→ Control Panel
```

### 5. Policy Refresh

Forced an immediate update using:

```cmd
gpupdate /force
```

### 6. FSMO Verification

Checked the FSMO role holders using:

```cmd
netdom query fsmo
```

Confirmed that all five FSMO roles were held by:

```text
server01
```

---

# Screenshots

> 📸 **Screenshot Placeholder**  
> **Filename:** `08-01-group-policy-management.png`  
> **Description:** Group Policy Management showing the `corp.danieltraining.com` domain and the existing Default Domain Policy.

> 📸 **Screenshot Placeholder**  
> **Filename:** `08-02-default-domain-policy.png`  
> **Description:** Default Domain Policy settings showing the existing password and account security configuration.

> 📸 **Screenshot Placeholder**  
> **Filename:** `08-03-disable-control-panel-gpo.png`  
> **Description:** Group Policy Management showing the `Disable Control Panel - End Users` GPO linked to the `End Users` OU.

> 📸 **Screenshot Placeholder**  
> **Filename:** `08-04-control-panel-policy.png`  
> **Description:** Group Policy Management Editor showing `Prohibit access to Control Panel and PC Settings` configured as Enabled.

> 📸 **Screenshot Placeholder**  
> **Filename:** `08-05-gpupdate-force.png`  
> **Description:** Command Prompt showing `gpupdate /force` being executed on the domain-joined computer.

> 📸 **Screenshot Placeholder**  
> **Filename:** `08-06-fsmo-roles.png`  
> **Description:** Command Prompt showing the `netdom query fsmo` output with all five FSMO roles assigned to `server01`.

---

# Key Skills Demonstrated

This section demonstrates practical experience with:

- Group Policy Management
- Group Policy Objects (GPOs)
- Active Directory OUs
- GPO linking
- User Configuration
- Administrative Templates
- Control Panel restrictions
- Centralised Windows administration
- Group Policy troubleshooting
- `gpupdate /force`
- Default Domain Policy
- Password policy review
- Account lockout policy review
- FSMO role identification
- `netdom query fsmo`
- Active Directory administration

---

# What I Learned

Group Policy was one of the areas of this project that helped me understand the real value of Active Directory.

Before working with it practically, the large number of available settings made Group Policy seem complicated. Building my own policy made the concept much easier to understand.

I learned that instead of configuring the same setting individually on every computer, I can configure it once, link the GPO to the appropriate OU, and allow Windows to distribute the setting automatically.

I also learned that understanding **where a policy is linked** is just as important as understanding the setting itself.

The `Disable Control Panel - End Users` policy was a simple example, but it demonstrated the same centralised-management principle used for much larger environments.

Checking the FSMO roles also gave me a better understanding of what happens behind the scenes in Active Directory and reinforced the importance of knowing which Domain Controller holds critical roles.

By the end of this section, I had practical experience creating, linking, configuring, testing, and troubleshooting Group Policy in my Windows Server 2025 domain.

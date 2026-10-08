# Microsoft Defender Antivirus

## Table of Contents

- [Overview](#overview)
- [Verifying Microsoft Defender Was Running](#verifying-microsoft-defender-was-running)
- [Running a Malware Scan](#running-a-malware-scan)
- [Updating Security Intelligence](#updating-security-intelligence)
- [Reviewing Microsoft Defender Settings](#reviewing-microsoft-defender-settings)
  - [Real-time Protection](#real-time-protection)
  - [Cloud-delivered Protection](#cloud-delivered-protection)
  - [Automatic Sample Submission](#automatic-sample-submission)
  - [Tamper Protection](#tamper-protection)
  - [Exclusions](#exclusions)
- [Microsoft Defender in Enterprise Environments](#microsoft-defender-in-enterprise-environments)
- [Verification and Expected Results](#verification-and-expected-results)
- [Screenshots](#screenshots)
- [What I Learned](#what-i-learned)

---

## Overview

After completing the core configuration of my Windows Server lab, I wanted to make sure the server was protected against malware and other security threats.

Windows Server 2025 comes with Microsoft Defender Antivirus built in, so I did not need to install additional antivirus software before securing the system.

While many organisations complement Defender with dedicated Endpoint Detection and Response (EDR) platforms, Microsoft Defender Antivirus provides a solid baseline level of protection. For my home lab, it provided enough functionality for me to practise the fundamentals of endpoint protection and understand how antivirus security is managed on Windows Server.

This section gave me practical experience with:

- Verifying that Microsoft Defender Antivirus was running.
- Performing Quick and Full malware scans.
- Updating Microsoft Defender security intelligence.
- Reviewing Real-time Protection.
- Reviewing Cloud-delivered Protection.
- Reviewing Automatic Sample Submission.
- Reviewing Tamper Protection.
- Reviewing antivirus Exclusions.
- Understanding how Microsoft Defender fits into a wider enterprise security environment.

> **Why this matters:** Antivirus protection is only useful when it is active, up to date, and properly configured. This exercise helped me understand how endpoint protection fits into the wider security of a Windows Server environment.

---

## Verifying Microsoft Defender Was Running

Before making any changes, I wanted to confirm that Microsoft Defender Antivirus was active and protecting the server.

### Step 1 — Open Windows Security

I opened the **Start** menu and searched for:

```text
Windows Security
```

I opened the Windows Security application.

### Step 2 — Check Security at a Glance

On the **Security at a glance** page, I checked the **Virus & threat protection** section.

It displayed a green tick, confirming that Microsoft Defender Antivirus was active and protecting the server.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/c68362bf-4839-47ec-afba-5f4c6417b2ab" />
 Windows Security showing the Virus & threat protection area as healthy with a green tick.

If Defender had displayed a warning or indicated that protection was turned off, I would have opened the section and enabled it before continuing.

### Step 3 — Check App & browser control

I also confirmed that **App & browser control** was enabled.

This provides another layer of protection against malicious applications and unsafe downloads.

Seeing the protection areas marked as healthy gave me confidence that the server's built-in security features were functioning as expected.

---

## Running a Malware Scan

With Microsoft Defender confirmed to be running, I performed a malware scan to verify that the server was free from known threats.

### Step 1 — Open Virus & threat protection

From **Windows Security**, I opened:

```text
Virus & threat protection
```

### Step 2 — Run a Quick Scan

I selected:

```text
Quick Scan
```

A Quick Scan focuses on the parts of Windows where malware is commonly found.

On my server, the scan completed in just a few minutes.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/19fc44d0-0682-4d25-97d3-eae02ce51d28" />
  Windows Security showing Virus & threat protection and the completed Quick Scan.

### Step 3 — Review Scan Options

I also opened **Scan options** to understand the other scanning methods available.

The available options included:

- **Quick scan** — checks areas of the system where threats are commonly found.
- **Full scan** — examines every file and running program on the attached drives.
- **Custom scan** — allows specific files or locations to be selected.
- **Microsoft Defender Offline scan** — designed to help identify and remove particularly difficult malware by scanning outside the normal Windows environment.

I selected the **Full Scan** option to understand how it differs from a Quick Scan.

Unlike a Quick Scan, a Full Scan examines every file on every drive attached to the server. It takes significantly longer, but provides a much more comprehensive inspection.

This helped me understand when each scan type would be appropriate.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/fc69533e-68b0-4469-883f-14544e33a8de" />
 Windows Security Scan options showing Quick Scan, Full Scan, Custom Scan, and Microsoft Defender Offline scan.

---

## Updating Security Intelligence

One of the most important things I learned during this section was that antivirus software is only effective when it can recognise current threats.

Microsoft Defender relies on regularly updated **security intelligence**, sometimes referred to as virus definitions, to identify newly discovered malware.

To make sure my server was using the latest protection information, I completed the following steps.

### Step 1 — Open Protection Updates

Within:

```text
Virus & threat protection
```

I scrolled down to:

```text
Virus & threat protection updates
```

### Step 2 — Check for Updates

I selected:

```text
Check for updates
```

If newer security intelligence was available, Windows automatically downloaded and installed it.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/714f1a37-df7e-4c19-ac0d-87b84b3aac09" />
 Virus & threat protection updates page showing the option to check for the latest security intelligence.

This reinforced an important point for me: keeping antivirus definitions up to date is just as important as keeping Windows itself updated.

New malware variants appear regularly, so current security intelligence helps Defender recognise and respond to newer threats.

---

## Reviewing Microsoft Defender Settings

To better understand how Microsoft Defender protects Windows Server in real time, I opened:

```text
Virus & threat protection
→ Manage settings
```

I reviewed the main protection features available.


<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/f7ee1c44-2772-403a-802f-86cb2871672b" />
 Microsoft Defender Antivirus Manage settings page showing the main protection features.

---

### Real-time Protection

The first setting I checked was **Real-time Protection**, which was enabled.

Real-time Protection continuously monitors files as they are:

- Created.
- Opened.
- Downloaded.
- Copied.
- Modified.

If Defender detects malicious activity, it can stop the threat before it has an opportunity to execute.

Because this provides continuous protection, I would leave it enabled unless there was a specific troubleshooting reason to temporarily disable it.

> **Key takeaway:** Real-time Protection provides continuous monitoring instead of relying only on manually scheduled scans.

---

### Cloud-delivered Protection

Next, I reviewed **Cloud-delivered Protection**.

Rather than relying only on locally stored malware definitions, this feature allows Defender to consult Microsoft's cloud-based threat intelligence when suspicious files are detected.

This allows Microsoft Defender to identify and respond to newly emerging threats faster than relying solely on traditional signature updates.

> **Key takeaway:** Cloud-delivered Protection extends the information available to the local antivirus engine by using Microsoft's cloud threat intelligence.

---

### Automatic Sample Submission

I also reviewed **Automatic Sample Submission**.

When enabled, Microsoft Defender can securely send suspicious files to Microsoft for further analysis.

This helps Microsoft improve malware detection and strengthen protection across Windows devices.

Although enabling this setting is optional, I could see how it contributes to faster identification of new threats.

> **Key takeaway:** Suspicious samples can provide Microsoft with additional information that helps improve threat detection.

---

### Tamper Protection

One of the features I found particularly useful was **Tamper Protection**.

This setting helps prevent malware or unauthorised users from disabling Microsoft Defender or modifying important security settings.

This is important because some malware attempts to disable antivirus protection before carrying out an attack.

Keeping Tamper Protection enabled therefore adds another layer of defence.

> **Security note:** Antivirus protection can only help if an attacker cannot easily disable the protection itself.

---

### Exclusions

Finally, I reviewed the **Exclusions** section.

Exclusions allow trusted files, folders, or applications to be ignored during antivirus scanning.

This can be useful when legitimate software is incorrectly identified by antivirus protection. However, I learned that exclusions need to be handled carefully.

Creating unnecessary exclusions could leave parts of the system unprotected.

For that reason, I would only create an exclusion after verifying that the application, file, or location is completely trusted and that excluding it is actually necessary.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/36278673-a78b-4582-8368-a3f842adc3cb" />
Microsoft Defender Exclusions page showing the available option to add or remove exclusions.

---

## Microsoft Defender in Enterprise Environments

As I researched Microsoft Defender further, I discovered that many organisations build on its antivirus capabilities with more advanced endpoint security platforms.

One example is **Microsoft Defender for Endpoint**.

It extends standard endpoint protection with capabilities such as:

- Centralised security management.
- Advanced behavioural threat detection.
- Threat intelligence.
- Automated investigation and response.
- Endpoint visibility across an organisation.

I also learned that organisations may use third-party Endpoint Detection and Response (EDR) solutions such as:

- CrowdStrike Falcon.
- SentinelOne.
- Sophos Intercept X.

The purpose of these platforms is to provide deeper visibility into endpoint activity and give security teams additional capabilities for detecting, investigating, and responding to sophisticated threats.

Microsoft's current Windows Server documentation confirms that Microsoft Defender Antivirus is supported on Windows Server 2025 and can be managed through Windows Server security tooling.

> **Important distinction:** My lab focused on Microsoft Defender Antivirus. I did not treat this home-lab configuration as a full Microsoft Defender for Endpoint deployment.

---

## Verification and Expected Results

After completing the exercise, I verified the following:

| Area | Expected Result |
|---|---|
| Windows Security | Opens successfully |
| Virus & threat protection | Shows as healthy/active |
| App & browser control | Enabled |
| Quick Scan | Completes successfully |
| Full Scan | Available through Scan options |
| Security intelligence | Can be checked for updates |
| Real-time Protection | Enabled |
| Cloud-delivered Protection | Reviewed |
| Automatic Sample Submission | Reviewed |
| Tamper Protection | Enabled |
| Exclusions | Reviewed carefully |

The objective was not simply to open Windows Security and confirm that it existed. I wanted to understand what each protection feature actually does and how those features contribute to the security of the server.

---
<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/40d1bcf5-f876-4c76-abfd-6ef99f17266d" />
Windows Security showing the current Microsoft Defender Antivirus protection status.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/90f41a00-540a-48d4-8947-22794e1e4454" />
Microsoft Defender Antivirus displaying the Quick Scan interface or completed scan results.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/4bdaa31a-10cf-4372-bbe8-a34e35fe758b" />
Microsoft Defender Antivirus showing the available scan options, including Quick, Full, Custom, and Microsoft Defender Offline scans.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/25c60ab7-1c4a-4c0e-91b3-dd275461c00b" />
Virus & threat protection updates page showing the current Security intelligence version and update status.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/e455a328-c15f-434f-a529-4c3d4dcc51be" />
Microsoft Defender Antivirus Manage settings page displaying real-time protection and related security options.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/aa056e2e-5cc3-4021-a550-834e9bf20acb" />
Microsoft Defender Antivirus Exclusions page showing files, folders, or processes excluded from antivirus scans.
---

## What I Learned

Completing this section gave me a much stronger understanding of Microsoft's built-in endpoint protection and how it fits into the overall security of a Windows Server environment.

Rather than simply relying on the default settings, I learned how to:

- Verify that Microsoft Defender Antivirus was operating correctly.
- Perform Quick and Full malware scans.
- Update security intelligence.
- Review Real-time Protection.
- Understand Cloud-delivered Protection.
- Understand Automatic Sample Submission.
- Understand the purpose of Tamper Protection.
- Review antivirus exclusions.
- Understand the difference between basic antivirus protection and enterprise EDR capabilities.

More importantly, this exercise reinforced that effective server security is not achieved through a single tool.

It comes from combining multiple layers of protection, including:

- Regular Windows updates.
- A properly configured firewall.
- Strong access controls.
- Active antivirus protection.
- Continuously updated security intelligence.
- Monitoring and investigation.

Microsoft Defender Antivirus forms an important part of that layered security approach. Working through this section strengthened my understanding of Windows Server administration while also giving me more practical experience with endpoint security.

---

## Skills Demonstrated

- Microsoft Defender Antivirus administration
- Windows Server security
- Malware scanning
- Quick and Full scan procedures
- Security intelligence updates
- Real-time protection
- Cloud-delivered protection
- Automatic sample submission
- Tamper Protection
- Antivirus exclusion management
- Endpoint security fundamentals
- EDR awareness
- Security verification and validation

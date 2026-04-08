# Module 11: Capstone Project & Certification Prep

---

## Module Overview

Welcome to the final module of the **Microsoft Intune Endpoint Administration** course. This capstone project is the culminating experience that ties together every concept, skill, and lab you have worked through in Modules 1–10. Rather than studying topics in isolation, you will now apply them holistically to a realistic enterprise scenario.

You will act as the lead endpoint administrator for **Contoso Ltd**, a mid-sized professional-services firm with 500 devices spread across three office locations and a fully remote workforce. Contoso is retiring its legacy on-premises Configuration Manager (SCCM) estate and migrating entirely to Microsoft Intune in the cloud. Your mission is to design, deploy, validate, and document that migration end-to-end.

In addition to the hands-on project work, this module prepares you for the **MD-102: Endpoint Administrator Associate** certification exam with simulated questions, exam-topic mapping, and a study roadmap.

**Estimated time to complete:** 8–12 hours  
**Difficulty:** Advanced  
**Prerequisites:** Modules 1–10 completed; Microsoft 365 E3/E5 trial tenant or lab environment

---

## Learning Objectives

By the end of this module you will be able to:

1. Design an end-to-end Intune deployment architecture for an enterprise of 500+ devices.
2. Configure a production-ready Microsoft 365 / Entra ID tenant for Intune management.
3. Enroll Windows 10/11, iOS/iPadOS, and Android devices using multiple enrollment methods.
4. Deploy a multi-tier application portfolio (Win32, Microsoft Store, LOB, web apps).
5. Build and assign compliance policies, conditional access rules, and endpoint security baselines.
6. Automate routine administrative tasks with Microsoft Graph API and PowerShell.
7. Generate executive-level reporting and custom analytics dashboards.
8. Demonstrate readiness for the MD-102 Endpoint Administrator Associate exam.

---

## Capstone Project: Contoso Ltd Migration

### Company Profile

| Attribute | Detail |
|---|---|
| Company name | Contoso Ltd |
| Industry | Professional services (legal & consulting) |
| Total devices | 500 |
| Windows laptops | 320 (mix of Windows 10 22H2 and Windows 11 23H2) |
| iOS/iPadOS devices | 120 (company-owned iPhones and iPads) |
| Android devices | 60 (Samsung Knox corporate devices) |
| Locations | Chicago HQ, New York Branch, London Branch, fully remote workers |
| Existing identity | Active Directory on-premises + Entra ID Connect (hybrid) |
| Current MDM | None – transitioning from Group Policy only |
| Compliance requirements | SOC 2 Type II, GDPR (London office), HIPAA-adjacent (healthcare clients) |

### Business Goals

- Eliminate all on-premises MDM infrastructure within 90 days.
- Enforce conditional access so only compliant, Intune-managed devices can reach corporate data.
- Deliver a self-service app portal to all employees.
- Achieve < 4-hour onboarding time for new hires using Windows Autopilot.
- Provide the security team with a real-time compliance dashboard.

---

## Project Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                     Microsoft 365 Cloud (Entra ID)                  │
│                                                                     │
│   ┌─────────────┐   ┌──────────────┐   ┌────────────────────────┐  │
│   │  Intune MDM  │   │  Defender    │   │  Conditional Access    │  │
│   │  & MAM       │   │  for Endpoint│   │  (Entra ID P2)         │  │
│   └──────┬──────┘   └──────┬───────┘   └───────────┬────────────┘  │
│          │                 │                        │               │
│   ┌──────▼─────────────────▼────────────────────────▼────────────┐  │
│   │              Microsoft Graph API / REST                       │  │
│   └──────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────┘
          │                    │                    │
          ▼                    ▼                    ▼
  ┌───────────────┐   ┌────────────────┐   ┌───────────────────┐
  │  Windows 11   │   │  iOS / iPadOS  │   │  Android (Knox)   │
  │  Autopilot    │   │  ADE + Apple   │   │  Zero-Touch /     │
  │  (320 devices)│   │  Configurator  │   │  Knox Mobile Enr. │
  │               │   │  (120 devices) │   │  (60 devices)     │
  └───────┬───────┘   └───────┬────────┘   └────────┬──────────┘
          │                   │                     │
          ▼                   ▼                     ▼
  ┌─────────────────────────────────────────────────────────────┐
  │              Contoso Corporate Network                       │
  │                                                             │
  │  Chicago HQ ──── New York Branch ──── London Branch        │
  │  (AD DS + ADFS)   (VPN Gateway)        (GDPR boundary)     │
  └─────────────────────────────────────────────────────────────┘
          │
          ▼
  ┌────────────────────────────────┐
  │  On-Premises Infrastructure    │
  │  • Active Directory DS         │
  │  • Entra ID Connect (sync)     │
  │  • Certificate Authority (PKI) │
  │  • SCCM (being retired)        │
  └────────────────────────────────┘
```

**Data flow summary:**
1. Device enrolls via Autopilot / ADE / Knox → Intune receives hardware identity.
2. Intune pushes configuration profiles, compliance policies, and apps.
3. Defender for Endpoint reports device health → Intune marks device compliant/non-compliant.
4. Conditional Access evaluates compliance + user risk before granting access to Exchange / SharePoint / Teams.
5. Graph API / PowerShell automation handles bulk operations and reporting.

---

## Labs

---

### Lab 11.1: Comprehensive Enterprise Deployment Project

**Scenario:** You are the sole Intune administrator at Contoso Ltd. Follow each phase to build out the complete environment.

**Duration:** 4–6 hours  
**See:** [`lab-guide.md`](./lab-guide.md) for the full step-by-step walkthrough covering all five phases.

#### Phase Summary

| Phase | Focus Area | Key Deliverable |
|---|---|---|
| 1 | Tenant & Infrastructure Setup | Configured tenant, licenses assigned, MDM authority set |
| 2 | Device Enrollment | Windows Autopilot, ADE for iOS, Knox for Android |
| 3 | Application Deployment | Win32 packager, Microsoft Store, web clips |
| 4 | Security & Compliance | Baselines, compliance policies, conditional access |
| 5 | Automation & Reporting | Graph PowerShell scripts, compliance dashboard |

---

### Lab 11.2: Simulate MD-102 / MS-101 Exam Questions

**Instructions:** Answer each question before revealing the answer. Track your score. A passing score on the actual exam is 700/1000.

---

**Question 1**  
A user's Windows 11 device is marked **Not Compliant** in Intune even though BitLocker is enabled. Which setting should you check first?

- A) The compliance policy grace period  
- B) The device enrollment type  
- C) The BitLocker encryption report method in the compliance policy  
- D) The Windows Defender firewall profile  

**Answer: C** — Intune checks BitLocker compliance through the compliance policy's *Require device encryption* setting; if the policy uses the wrong report method (e.g., requires OS drive encryption via ConfigMgr reporting instead of Intune), the device may show as non-compliant despite BitLocker being active.

---

**Question 2**  
Contoso wants to prevent corporate data from being copied from the Outlook mobile app to a personal notes app on iOS. Which Intune feature should you configure?

- A) Device compliance policy  
- B) App protection policy (MAM)  
- C) Device configuration profile – restrictions  
- D) Conditional access policy  

**Answer: B** — App protection policies (MAM) control data transfer between managed and unmanaged apps without requiring device enrollment.

---

**Question 3**  
You need to deploy a legacy 32-bit `.exe` installer to 200 Windows devices. The application requires silent installation switches. What is the correct Intune app type to use?

- A) Microsoft Store app  
- B) Line-of-business app (.msi)  
- C) Win32 app  
- D) Web app  

**Answer: C** — Win32 apps support `.exe` installers, custom install/uninstall commands, detection rules, and dependency chains.

---

**Question 4**  
Which enrollment method allows Windows devices to be pre-configured and delivered directly to end users without IT touching the hardware?

- A) Bulk enrollment via provisioning package  
- B) Windows Autopilot user-driven mode  
- C) Co-management with Configuration Manager  
- D) Entra ID join via Settings app  

**Answer: B** — Windows Autopilot user-driven mode enables zero-touch provisioning: the OEM or reseller registers the hardware hash, and the device self-configures on first boot.

---

**Question 5**  
An iOS device enrolled via ADE is showing *Supervised: No* in the Intune console. What is the most likely cause?

- A) The Apple MDM Push certificate expired  
- B) The device was not assigned to the MDM server in Apple Business Manager  
- C) The enrollment profile was not set to supervised mode  
- D) The user declined the management profile  

**Answer: C** — The ADE enrollment profile must explicitly enable supervised mode; without it, devices enroll as unsupervised even via ADE.

---

**Question 6**  
Contoso's conditional access policy requires compliant devices. A user with a compliant device cannot access SharePoint Online. What should you check?

- A) The user's license assignment  
- B) The conditional access policy assignment (users/groups scope)  
- C) The SharePoint Online service health  
- D) The device's Intune enrollment date  

**Answer: B** — Conditional access policies apply only to the users/groups included in the policy assignment; if the user is excluded or not included, the policy does not enforce compliance for them.

---

**Question 7**  
Which Intune report provides a per-app installation status broken down by device and user?

- A) Device compliance report  
- B) App installation status report  
- C) Endpoint analytics baseline  
- D) Defender for Endpoint threat report  

**Answer: B** — The App installation status report (Intune > Apps > Monitor > App install status) shows install state per app, per device, and per user.

---

**Question 8**  
You want to enforce a minimum OS version of Windows 11 22H2 for compliance. Where do you configure this?

- A) Device configuration profile – Edition upgrade  
- B) Windows Update for Business ring  
- C) Device compliance policy – Device health section  
- D) Device compliance policy – Device properties section  

**Answer: D** — Minimum and maximum OS version requirements are in the **Device properties** section of a Windows compliance policy.

---

**Question 9**  
A SCEP certificate profile is not deploying to devices. Which service is responsible for translating Intune certificate requests to your on-premises CA?

- A) Intune Connector for Active Directory  
- B) Network Device Enrollment Service (NDES) + Microsoft Intune Certificate Connector  
- C) Azure AD Application Proxy  
- D) Windows Push Notification Service (WNS)  

**Answer: B** — SCEP certificate delivery requires NDES and the Microsoft Intune Certificate Connector installed on an on-premises server that can reach the CA.

---

**Question 10**  
Contoso needs to remotely wipe corporate data from a personal (BYOD) iPhone without erasing the user's personal photos. Which action should you use?

- A) Retire  
- B) Wipe  
- C) Delete  
- D) Fresh Start  

**Answer: A** — **Retire** removes corporate data, profiles, and apps managed by Intune while leaving personal data intact. **Wipe** performs a factory reset.

---

**Question 11**  
Which Graph API permission scope is required to read all device compliance policies programmatically?

- A) `DeviceManagementConfiguration.Read.All`  
- B) `DeviceManagementApps.Read.All`  
- C) `Directory.Read.All`  
- D) `Policy.Read.All`  

**Answer: A** — Compliance policies are part of the device management configuration namespace; `DeviceManagementConfiguration.Read.All` grants read access.

---

**Question 12**  
You deploy an Endpoint Security – Antivirus policy. Which underlying technology enforces the settings on Windows 11?

- A) Windows Defender Application Control (WDAC)  
- B) Microsoft Defender Antivirus via the Defender CSP  
- C) Windows Security Center GPO templates  
- D) Microsoft Endpoint Configuration Manager client  

**Answer: B** — Endpoint Security antivirus policies in Intune use the **Defender CSP** to configure Microsoft Defender Antivirus settings on Windows devices.

---

**Question 13**  
Contoso wants kiosk-mode devices in the lobby running a single UWP app. Which configuration profile type should you use?

- A) Device restrictions – General  
- B) Kiosk (single-app, multi-app)  
- C) Shared device configuration  
- D) Administrative templates (ADMX)  

**Answer: B** — The **Kiosk** profile type (under Device Configuration > Profiles > Windows > Kiosk) supports single-app and multi-app kiosk configurations.

---

**Question 14**  
A new Android device fails to enroll with the error "Device limit reached." Where do you adjust this limit?

- A) Entra ID – Device settings – Maximum number of devices  
- B) Intune – Enrollment – Enrollment restrictions – Device limit restriction  
- C) Intune – Devices – All devices – Enrollment limits  
- D) Both A and B  

**Answer: D** — Both Entra ID device limit and Intune enrollment device limit restrictions can independently block enrollment; both must be checked.

---

**Question 15**  
Which Windows Autopilot deployment mode is best for pre-provisioning devices in a staging area before shipping to users?

- A) User-driven Azure AD join  
- B) Self-deploying mode  
- C) Pre-provisioning (White Glove)  
- D) Co-management enrollment  

**Answer: C** — **Pre-provisioning (White Glove)** allows IT or a reseller to complete the device-side provisioning phase in advance so users only complete the user-side phase on first login.

---

**Question 16**  
You want to block users from unenrolling their corporate-owned iOS devices. Which setting controls this?

- A) Device compliance policy – Jailbreak detection  
- B) Enrollment restrictions – Platform restrictions  
- C) ADE enrollment profile – User affinity setting  
- D) Device configuration profile – Supervised restrictions – Allow unenrollment  

**Answer: D** — On supervised iOS devices, the **Device configuration profile > Restrictions > Allow unenrollment** setting (set to Block) prevents users from removing the management profile.

---

**Question 17**  
Endpoint Analytics shows a high startup-performance score regression on 40 devices after a Windows update. What is the most efficient Intune action?

- A) Wipe and re-enroll all 40 devices  
- B) Use Update Compliance to pause the update ring  
- C) Pause the Windows Update for Business ring that delivered the update  
- D) Create a PowerShell script to roll back the update  

**Answer: C** — Windows Update for Business rings in Intune support pausing; pausing the ring that pushed the problematic update stops it from deploying to remaining devices and allows investigation.

---

**Question 18**  
A user reports that a required app shows "Pending" for more than 24 hours. Which logs should you collect from the device first?

- A) Windows Event Viewer – Application log  
- B) Intune Management Extension (IME) log at `%ProgramData%\Microsoft\IntuneManagementExtension\Logs`  
- C) MDM Diagnostic Report via Settings > Accounts > Access work or school  
- D) Both B and C  

**Answer: D** — IME logs detail Win32/PowerShell app delivery errors; the MDM Diagnostic Report shows enrollment and policy state. Both are needed for complete triage.

---

**Question 19**  
Contoso must ensure devices automatically re-enroll after a corporate wipe. Which feature provides this for Windows Autopilot devices?

- A) Autopilot Reset  
- B) Enrollment Status Page retry  
- C) Windows Hello for Business re-provisioning  
- D) Fresh Start  

**Answer: A** — **Autopilot Reset** wipes the device and re-runs the Autopilot provisioning experience, restoring it to a business-ready state without removing it from the Autopilot hardware database.

---

**Question 20**  
Which Intune role has permissions to assign policies and profiles but cannot delete or create new ones?

- A) Intune Service Administrator  
- B) Help Desk Operator  
- C) Read Only Operator  
- D) Policy and Profile Manager  

**Answer: D** — The built-in **Policy and Profile Manager** role can read, create, update, and assign profiles/policies but does not include device wipe or delete permissions; check exact role definitions in your tenant as Microsoft updates built-in roles periodically.

---

**Score yourself:**  
- 18–20 correct: Exam-ready  
- 14–17 correct: Review weak areas  
- Below 14: Revisit relevant modules before sitting the exam

---

### Lab 11.3: Hands-on Review of Key Scenarios

---

#### Scenario 1: Device Marked Non-Compliant After Policy Change

**Situation:** You updated the compliance policy to require Windows 11 23H2 minimum OS. Fifty devices are now non-compliant. Conditional access is blocking those users from email. HR is calling.

**Diagnosis steps:**
1. In the Intune portal, navigate to **Reports > Device compliance > Non-compliant devices** and filter by OS version.
2. Confirm whether devices are genuinely on an older OS or whether a grace period is in effect.
3. Check **Devices > Monitor > Compliance policy settings** to see per-setting non-compliance.

**Resolution:**
1. Create a Windows Update for Business **feature update** policy targeting Windows 11 23H2 and assign it to the affected group.
2. Set a **7-day grace period** on the compliance policy's non-compliance action to email users instead of immediately blocking access.
3. Monitor the **Windows Feature Update report** to track upgrade progress.
4. Once devices reach 23H2, compliance status resolves automatically.

**Key lesson:** Always set a grace period when introducing OS version requirements mid-deployment; this avoids immediate productivity disruption.

---

#### Scenario 2: Win32 App Stuck in "Pending Install" State

**Situation:** A required Win32 app shows "Pending Install" on 30 devices for 48 hours. The IME service appears to be running.

**Diagnosis steps:**
1. On an affected device open `%ProgramData%\Microsoft\IntuneManagementExtension\Logs\IntuneManagementExtension.log`.
2. Search for the app's name or its Intune app ID; look for error codes.
3. Common error: `0x80070005` (Access denied during install) or `0x87D30003` (App supersedence conflict).

**Resolution:**
1. If access denied: ensure the install command runs as SYSTEM and the installer does not require user interaction.
2. If supersedence conflict: review app supersedence and dependency chains in **Apps > [App] > Properties > Supersedence**.
3. Re-upload the `.intunewin` package if the content hash mismatch error appears.
4. Force a sync from the device: **Settings > Accounts > Access work or school > Info > Sync**.

**Key lesson:** Win32 apps run as SYSTEM by default. Interactive installers that display UI will hang silently.

---

#### Scenario 3: Conditional Access Blocking a Compliant Device

**Situation:** A senior partner's laptop shows Compliant in Intune but conditional access blocks access to SharePoint. The user is exempt from MFA.

**Diagnosis steps:**
1. In Entra ID, go to **Monitoring > Sign-in logs** and find the blocked sign-in. Expand **Conditional Access** tab.
2. Identify which CA policy applied and what condition failed (e.g., compliant device, approved app).
3. Check whether the device is showing in Entra ID as registered and hybrid-joined correctly.

**Resolution:**
1. If the device registration is stale, run `dsregcmd /status` on the device; re-run hybrid join if needed.
2. If the CA policy has a conflicting grant control (e.g., requires approved client app AND compliant device as separate grants rather than either/or), update the grant logic.
3. If the user is in an exclusion group that should be included, fix group membership.
4. Use the **What If** tool in Conditional Access to simulate the user's sign-in and confirm expected policy behavior.

**Key lesson:** Use the CA **What If** tool proactively before rolling out new policies to validate expected behavior.

---

#### Scenario 4: iOS Devices Not Receiving Configuration Profiles

**Situation:** A new VPN profile assigned to the **London-iOS-Devices** group is not appearing on 40 iOS devices. The group is correctly populated.

**Diagnosis steps:**
1. Check **Devices > Configuration profiles > [Profile] > Device status** to see which devices show *Not Applicable* vs *Pending*.
2. Verify the assignment filter; if an assignment filter is applied, check whether the device properties match.
3. Check the Apple APNs certificate expiration: **Tenant administration > Connectors and tokens > Apple MDM Push certificate**.

**Resolution:**
1. If APNs certificate expired: renew immediately with the same Apple ID used originally; all iOS/macOS management stops if APNs lapses.
2. If filter mismatch: edit the assignment filter rule to include the device properties of the London fleet (e.g., `(device.manufacturer -eq "Apple") and (device.enrollmentProfileName -eq "London-ADE-Profile")`).
3. Force an APNs push: **Devices > [Device] > Sync**.

**Key lesson:** APNs certificate expiration silently breaks all iOS management. Set a calendar reminder 60 days before expiry.

---

#### Scenario 5: Autopilot Deployment Fails at Enrollment Status Page

**Situation:** New Windows 11 laptops for Contoso fail during the Autopilot OOBE at the Enrollment Status Page (ESP) with error `0x800705b4` (timeout).

**Diagnosis steps:**
1. Review the ESP log at `C:\Windows\ServiceProfiles\LocalService\AppData\Local\Temp\MdmDiagnostics`.
2. Identify which tracking category timed out: Device setup vs. Account setup.
3. Common causes: a large Win32 app with slow download, a PowerShell script exceeding timeout, or a certificate deployment failure.

**Resolution:**
1. If a Win32 app is causing the timeout: increase the ESP timeout (default 60 min) under **Enrollment > Enrollment Status Page > [Profile] > Settings > Time limit**.
2. Move non-critical apps to post-ESP delivery by setting them as **Available** rather than **Required**, or excluding them from ESP tracking.
3. If a PowerShell script times out: optimize the script or set `runAs = SYSTEM` with `enforceSignatureCheck = false` to remove signing overhead in the lab.
4. Test by capturing an Autopilot diagnostics ZIP: at the OOBE failure screen, press `Shift+F10` to open cmd, then run `mdmdiagnosticstool.exe -out C:\autopilot-diag`.

**Key lesson:** Always test ESP deployments in a pilot ring with a representative app and policy set before broad rollout.

---

## Certification Preparation

### MD-102: Endpoint Administrator Associate

**Exam overview:**

| Item | Detail |
|---|---|
| Exam code | MD-102 |
| Full name | Microsoft 365 Endpoint Administrator |
| Passing score | 700 / 1000 |
| Duration | 100 minutes |
| Question types | Multiple choice, case studies, drag-and-drop, active screen |
| Languages | English and others (see Microsoft Learn) |
| Cost | USD $165 (varies by region) |
| Renewal | Free online annual renewal assessment |

### Exam Skill Areas

| Domain | Weight |
|---|---|
| Deploy and manage Entra ID identities | ~15% |
| Manage, maintain, and protect devices | ~40% |
| Manage and protect apps | ~25% |
| Plan and manage compliance and conditional access | ~20% |

### Study Tips

1. **Hands-on labs beat passive reading.** Microsoft Learn sandbox environments are free; use them for every topic.
2. **Use Microsoft Learn skill measurements** (the built-in practice assessments on the exam page) to identify weak domains.
3. **Read the official Study Guide** available on Microsoft Learn for MD-102; it maps directly to exam objectives.
4. **Focus on "why" not just "how."** Exam questions frequently test reasoning (e.g., *which is the best option for this scenario*) rather than step-by-step recall.
5. **Know the differences** between: Wipe vs Retire, MAM with enrollment vs without, Autopilot modes, co-management workloads.
6. **Review change logs.** Intune updates monthly; check the [Intune What's New](https://learn.microsoft.com/en-us/mem/intune/fundamentals/whats-new) page regularly.
7. **Take timed practice tests** to simulate exam pressure; aim for at least three full practice exams before sitting.

### Practice Resources

| Resource | URL / Location |
|---|---|
| Microsoft Learn – MD-102 path | `learn.microsoft.com/certifications/exams/md-102` |
| Official practice assessment | Available free on the exam page |
| Microsoft 365 Developer tenant (free 90-day) | `developer.microsoft.com/microsoft-365/dev-program` |
| Intune documentation | `learn.microsoft.com/mem/intune` |
| Endpoint Manager community | `techcommunity.microsoft.com/t5/microsoft-intune` |
| MeasureUp practice exams (paid) | `measureup.com` |
| Whizlabs / Udemy MD-102 courses | Various |

---

## Course Completion Checklist

Use this checklist to confirm you have covered every major topic in the course before attempting the certification exam.

### Module Coverage

- [ ] **Module 01** – Explained the role of modern endpoint management and Microsoft Intune's place in the Microsoft 365 stack.
- [ ] **Module 02** – Navigated the Intune admin center; understood licensing, tenants, and MDM authority.
- [ ] **Module 03** – Configured Entra ID, Apple APNs, Android Enterprise, and Windows Autopilot prerequisites.
- [ ] **Module 04** – Enrolled Windows, iOS, and Android devices using at least two enrollment methods each.
- [ ] **Module 05** – Deployed Win32, Microsoft Store, LOB, and web apps; configured app protection policies.
- [ ] **Module 06** – Created compliance policies, configured conditional access, applied endpoint security baselines.
- [ ] **Module 07** – Used Remote Help and performed remote actions (sync, wipe, retire, reset passcode).
- [ ] **Module 08** – Built custom reports, exported compliance data, reviewed Endpoint Analytics.
- [ ] **Module 09** – Automated Intune tasks using Microsoft Graph PowerShell; explored Power Automate connectors.
- [ ] **Module 10** – Applied enterprise best practices, reviewed a production-ready deployment checklist.
- [ ] **Module 11** – Completed the Contoso capstone project across all five phases; scored ≥ 14/20 on practice questions.

### Skills Verification

- [ ] Can enroll a Windows device via Autopilot from scratch (hardware hash upload to first desktop).
- [ ] Can create and assign a compliance policy with conditional access integration.
- [ ] Can package and deploy a Win32 `.exe` application with detection rules.
- [ ] Can configure an app protection policy for MAM-WE on iOS.
- [ ] Can generate a device compliance report and export it to CSV.
- [ ] Can write a PowerShell script using the Microsoft Graph SDK to list non-compliant devices.
- [ ] Can troubleshoot a failed Autopilot deployment using ESP logs.
- [ ] Can renew the Apple APNs certificate without disrupting existing enrollments.

---

## Next Steps After Course Completion

1. **Schedule your MD-102 exam** at [Pearson VUE](https://home.pearsonvue.com/microsoft). Book 1–2 weeks out to maintain momentum.
2. **Build a portfolio:** Document your Contoso capstone project with screenshots and architecture notes. This is valuable evidence for job applications and performance reviews.
3. **Explore advanced paths:**
   - **SC-300** – Microsoft Identity and Access Administrator (deepens Entra ID/conditional access skills)
   - **SC-400** – Microsoft Information Protection Administrator (deepens data governance)
   - **AZ-104** – Azure Administrator Associate (broadens cloud infrastructure skills)
   - **MS-700** – Microsoft Teams Administrator (endpoint management intersects heavily with Teams devices)
4. **Join the community:** The [Microsoft Tech Community – Intune](https://techcommunity.microsoft.com/t5/microsoft-intune) forum is where product engineers post updates and answer questions.
5. **Set up Intune alerts** in your production tenant for certificate expiration, enrollment failures, and compliance drift so you stay ahead of issues proactively.
6. **Review the Intune What's New page monthly** — Intune ships updates every month and exam content can reflect recent changes.

---

*Congratulations on completing the Microsoft Intune Endpoint Administration course. You now have the knowledge, hands-on experience, and exam preparation to succeed as a Microsoft 365 Endpoint Administrator.*

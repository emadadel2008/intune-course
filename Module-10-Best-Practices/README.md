# Module 10: Best Practices & Real-World Scenarios

## Module Overview
This module synthesizes everything covered in the course into actionable best practices and real-world scenarios drawn from enterprise Intune deployments. You will learn how to design scalable group structures, build phased rollout strategies, avoid common pitfalls, and establish governance frameworks that stand up to audit. The guidance here reflects lessons learned from production environments across hundreds to thousands of managed endpoints.

---

## Learning Objectives
By the end of this module, you will be able to:

- **Design** a scalable Azure AD group structure with dynamic rules for Intune policy targeting
- **Define** a naming convention for policies, profiles, apps, and groups that survives organisational growth
- **Build** a phased (ring-based) deployment strategy for applications and OS updates to reduce risk
- **Identify** the top 10 most common Intune mistakes and implement controls to avoid them
- **Develop** a change management and communication plan for Intune rollouts
- **Create** runbooks and operational documentation for day-to-day administration
- **Implement** a Role-Based Access Control (RBAC) model aligned to least-privilege principles
- **Troubleshoot** application installation failures using event logs, IME logs, and diagnostic data
- **Audit** security policies and generate remediation plans from compliance reports

---

## Key Topics

### 1. Policy Organization and Naming Conventions

A consistent naming convention is the single most impactful best practice you can adopt. Without it, a tenant with 200+ policies becomes unmanageable within months.

#### Recommended Naming Schema

```
{Platform}-{PolicyType}-{Description}-{Scope}
```

| Token | Values | Example |
|---|---|---|
| Platform | WIN, MAC, IOS, AND, ALL | `WIN` |
| PolicyType | CFG (config), COMP (compliance), APP (app protection), SEC (security baseline), UPD (update ring) | `CFG` |
| Description | Short business-readable name, PascalCase | `WiFiCorporate` |
| Scope | All, Pilot, Corp, BYOD, Kiosk, Exec | `All` |

#### Full Naming Examples

- `WIN-CFG-WiFi-Corporate-All` — Windows configuration profile for corporate Wi-Fi, all devices
- `WIN-COMP-Baseline-Corp` — Windows compliance policy for corporate-owned devices
- `IOS-APP-OutlookProtection-BYOD` — iOS app protection policy for Outlook on BYOD
- `WIN-SEC-Defender-Baseline-All` — Windows security baseline for Microsoft Defender
- `WIN-UPD-FeatureUpdate-Pilot` — Windows feature update ring for pilot group
- `MAC-CFG-FileVault-Corp` — macOS FileVault encryption config for corporate Macs
- `AND-COMP-DeviceHealth-BYOD` — Android compliance policy for BYOD enrollment
- `ALL-CFG-CertSCEP-Corp` — Cross-platform SCEP certificate profile for corporate devices
- `WIN-CFG-Defender-ATP-All` — Windows Defender ATP onboarding profile, all devices

#### Policy Description Field
Always populate the **Description** field with:
- Business justification
- Ticket/change number
- Owner team
- Date last reviewed

Example:
```
Purpose: Enforce BitLocker encryption on all corporate Windows devices.
Owner: Endpoint Security Team
Change: CHG-2024-0412
Last Reviewed: 2024-10-01
```

---

### 2. Group Structure Design

#### Group Types in Azure AD / Entra ID

| Type | Use Case | Managed By |
|---|---|---|
| Assigned (static) | Pilot rings, named VIPs, break-glass accounts | Admin manually |
| Dynamic Device | All Windows, all iOS, all compliant | Rules engine |
| Dynamic User | All employees, department-based | HR attribute sync |
| Nested | Broad rollout groups built from smaller rings | Admin |

#### Recommended Group Hierarchy

```
GRP-Intune-All-Devices              <- catch-all dynamic (deviceManagementAppRegistrationTimestamp != null)
+-- GRP-Intune-Windows-All          <- dynamic (operatingSystem eq 'Windows')
|   +-- GRP-Intune-Win-Corporate    <- dynamic + Company ownership filter
|   |   +-- GRP-Intune-Win-Ring0-IT       <- assigned (IT staff, ~20 devices)
|   |   +-- GRP-Intune-Win-Ring1-Pilot    <- assigned (~100 early adopters)
|   |   +-- GRP-Intune-Win-Ring2-Broad    <- nested: Ring0 + Ring1 + remaining
|   +-- GRP-Intune-Win-BYOD         <- dynamic + Personal ownership
+-- GRP-Intune-iOS-All
+-- GRP-Intune-Android-All
+-- GRP-Intune-macOS-All
```

#### Dynamic Group Rule Examples

All corporate Windows devices:
```
(device.operatingSystem -eq "Windows") and (device.deviceOwnership -eq "Company")
```

All iOS devices enrolled via a specific profile:
```
(device.operatingSystem -eq "iOS") and (device.enrollmentProfileName -ne null)
```

All users in the Finance department:
```
(user.department -eq "Finance")
```

All devices with a specific device category:
```
(device.deviceCategory -eq "Kiosk")
```

#### Device Categories
Use device categories to auto-assign devices to groups at enrollment:

| Category | Purpose | Example Group Assignment |
|---|---|---|
| `Kiosk` | Shared/unattended devices | GRP-Intune-Kiosk-Devices |
| `Executive` | VIP devices with tailored policy | GRP-Intune-Exec-Devices |
| `FieldWorker` | Ruggedized/offline-capable builds | GRP-Intune-Field-Devices |
| `Developer` | Less-restrictive policy set | GRP-Intune-Dev-Devices |
| `Corporate-Standard` | Default employee build | GRP-Intune-Corp-Standard |

---

### 3. Application Deployment Strategy

#### Deployment Rings Model

Never deploy directly to production. Use a ring-based model to catch issues before broad impact.

```
Ring 0 (IT/Ops)     ---  ~20 devices     ---  Immediate deployment, no deferral
Ring 1 (Pilot)      ---  ~5-10% fleet    ---  3-7 day deferral after Ring 0 sign-off
Ring 2 (Early)      ---  ~20% fleet      ---  7-14 day deferral after Ring 1 sign-off
Ring 3 (Broad)      ---  Remaining fleet ---  14-21 day deferral after Ring 2 sign-off
```

#### Application Assignment Intent

| Intent | When to Use |
|---|---|
| **Required** | Mandatory software; installs silently without user intervention |
| **Available** | Optional apps; user installs via Company Portal |
| **Uninstall** | Remove an app from targeted devices |
| **Available (no enrollment)** | MAM-only scenarios for BYOD |

#### Win32 App Deployment Best Practices
- Always use **detection rules** (file, registry, or MSI product code) — never rely solely on exit codes
- Set **restart behaviour** intentionally: most business apps should use `Suppress any required device restarts`
- Use **supersedence** to replace older versions cleanly rather than creating parallel policies
- Set **install deadline** for Required apps so they do not sit pending indefinitely
- Test the `.intunewin` package on a clean VM before uploading to the Intune portal

#### Update Ring Strategy for Windows

```
WIN-UPD-Quality-Ring0-IT        <- 0 days deferral
WIN-UPD-Quality-Ring1-Pilot     <- 7 days deferral
WIN-UPD-Quality-Ring2-Broad     <- 14 days deferral
WIN-UPD-Feature-Ring0-IT        <- 0 days deferral, manual approval
WIN-UPD-Feature-Ring1-Pilot     <- 30 days deferral
WIN-UPD-Feature-Ring2-Broad     <- 60 days deferral
```

---

### 4. Common Intune Mistakes and How to Avoid Them

#### Top 10 Intune Mistakes

1. **Deploying to "All Devices" or "All Users" immediately**
   - *Risk*: One misconfigured policy can lock out or break your entire fleet.
   - *Fix*: Always target Ring 0 first. Validate, then expand ring by ring.

2. **No naming convention on policies or groups**
   - *Risk*: Duplicate policies, conflicting settings, impossible to audit.
   - *Fix*: Enforce the `{Platform}-{Type}-{Description}-{Scope}` schema before Day 1.

3. **Ignoring policy conflicts and merge behaviour**
   - *Risk*: Security settings silently overridden by a conflicting profile.
   - *Fix*: Use the Intune Policy Conflict report regularly; understand that most-restrictive wins for compliance, but configuration profiles conflict and neither setting applies.

4. **Using "Available" intent for security-critical software**
   - *Risk*: Endpoint protection tools never install because users ignore Company Portal.
   - *Fix*: Use **Required** intent for security agents (EDR, VPN, certificates).

5. **Not documenting who changed what, when**
   - *Risk*: Unexplained compliance drops, no audit trail for security reviews.
   - *Fix*: Enable Audit Logs in Intune and integrate with Azure Monitor / Log Analytics.

6. **Over-relying on device compliance without Conditional Access enforcement**
   - *Risk*: Non-compliant devices still access corporate resources.
   - *Fix*: Connect compliance policies to Conditional Access policies in Entra ID.

7. **Not staging enrollment profiles before bulk enrollment**
   - *Risk*: Devices land in the wrong group, receive the wrong policy set.
   - *Fix*: Pre-assign device categories and enrollment profiles before imaging or shipping.

8. **Forgetting to set enrollment restrictions**
   - *Risk*: Unsupported OS versions, personal devices, or unlocked bootloaders enroll.
   - *Fix*: Configure enrollment restrictions to block unsupported platforms and OS versions.

9. **Deploying PowerShell scripts without testing**
   - *Risk*: Scripts fail silently, leaving devices in an unknown state with no visibility.
   - *Fix*: Test scripts in Ring 0 with verbose logging; monitor script run status in device view.

10. **Never reviewing stale policies and apps**
    - *Risk*: Hundreds of orphaned policies, ghost groups, and retired apps still assigned.
    - *Fix*: Schedule a quarterly hygiene review to archive policies that no longer apply.

---

### 5. Change Management and Communication

A successful Intune rollout is 50% technical and 50% people. Without communication, even a technically perfect rollout generates avoidable support tickets and user friction.

#### Change Management Framework

**Phase 1 — Awareness (4 weeks before rollout)**
- Announce the change via email, intranet, and leadership cascades
- Explain *why* the change is happening (security posture, compliance, user experience)
- Publish an FAQ document on the intranet or SharePoint

**Phase 2 — Pilot (2 weeks before broad rollout)**
- Enroll Ring 0 (IT) and Ring 1 (Pilot) users
- Collect feedback via a short survey or dedicated Teams channel
- Document and resolve blockers before proceeding to broad rollout

**Phase 3 — Broad Rollout**
- Communicate the rollout date at least 5 business days in advance
- Set up a dedicated support channel (Teams/Slack/ServiceNow queue)
- Monitor compliance dashboards daily for the first two weeks

**Phase 4 — Stabilisation**
- Close the dedicated support channel after 30 days
- Publish a lessons-learned document
- Schedule a 90-day follow-up review to measure adoption and remaining issues

#### Stakeholder Communication Template

```
Subject: [Action Required] Endpoint Management Upgrade — [Date]

Your device will be enrolled in our new management platform on [Date].
What to expect:
  - A short enrollment prompt on your device
  - Company Portal installation (automatic)
  - No data on your personal storage will be accessed

Action needed: Please ensure your device is connected to power and the
internet on [Date].

Questions? Contact [support alias] or visit [FAQ link].
```

---

### 6. Documentation and Runbooks

#### Essential Runbooks to Maintain

| Runbook | Contents |
|---|---|
| **Enrollment Runbook** | Step-by-step enrollment for each platform, screenshots, troubleshooting steps |
| **Policy Change Runbook** | How to propose, test, and deploy a new policy (approvals, change window, rollback) |
| **App Packaging Runbook** | How to package a Win32 app, create `.intunewin`, upload, and validate detection |
| **Break-Glass Runbook** | What to do if Conditional Access locks out all admins |
| **Offboarding Runbook** | How to retire/wipe a device, revoke licenses, remove from groups |
| **Incident Response Runbook** | Steps when a device is reported lost or stolen |
| **Quarterly Hygiene Runbook** | How to identify and archive stale policies, groups, and apps |

#### Documentation Minimum Standards
Every policy, app, and group in your tenant should have:
- **Name** — follows the naming convention
- **Description** — business purpose, owner, ticket reference, review date
- **Assignment** — target groups documented and justified
- **Dependencies** — what else must be in place for this object to function correctly

#### Suggested Documentation Tooling
- **Wiki / SharePoint** — policy registry, runbooks, architecture diagrams
- **Azure Monitor Workbook** — live compliance and deployment dashboards
- **Azure DevOps / GitHub** — store Intune configuration exports, track changes via pull requests
- **ServiceNow / Jira** — change requests, incidents, problem records

---

### 7. Governance and RBAC Design

#### Principle of Least Privilege in Intune

Intune RBAC allows you to grant only the permissions a role truly needs. Avoid assigning the built-in Intune Service Administrator role broadly — it grants full control over all Intune objects.

#### Recommended Custom Roles

| Role Name | Permissions | Assigned To |
|---|---|---|
| `Intune-Read-Only` | Read all Intune data | Help Desk Tier 1, Auditors |
| `Intune-HelpDesk` | Retire, wipe, reset passcode, sync device | Help Desk Tier 2 |
| `Intune-AppAdmin` | Create/edit/delete apps and app policies | Application Packaging Team |
| `Intune-PolicyAdmin` | Create/edit/delete config and compliance policies | Endpoint Engineering |
| `Intune-EnrollmentAdmin` | Manage enrollment profiles and device categories | Provisioning Team |
| `Intune-FullAdmin` | All permissions | Senior Engineers only, MFA-enforced |

#### Scope Tags
Use Scope Tags to limit admins to managing only the devices they are responsible for:

- `ScopeTag-Region-EMEA` — EMEA admins only see EMEA-tagged devices
- `ScopeTag-Department-Finance` — Finance IT sees only Finance devices
- `ScopeTag-Platform-iOS` — Mobile team sees only iOS and Android

#### RBAC Governance Checklist
- [ ] All admin accounts are cloud-only (not synced) and protected by MFA + PIM
- [ ] No shared admin accounts — each person has their own named role assignment
- [ ] Privileged access reviewed quarterly via Entra ID Access Reviews
- [ ] Emergency access (break-glass) accounts documented, secured, and monitored
- [ ] Audit logs shipped to Log Analytics workspace with 90-day minimum retention

---

## Hands-On Labs

### Lab 10.1: Design an Effective Group Structure

**Objective:** Create dynamic Azure AD groups using device and user properties, configure device categories, and produce a written naming convention document.

**Steps:**

1. Sign in to [https://entra.microsoft.com](https://entra.microsoft.com). Navigate to **Groups** > **All groups** > **+ New group**.

2. Set **Group type** to `Security` and **Membership type** to `Dynamic Device`. Name the group `GRP-Intune-Win-Corporate` and click **Add dynamic query**.

3. Enter the following rule and click **Save**, then **Create**:
   ```
   (device.operatingSystem -eq "Windows") and (device.deviceOwnership -eq "Company")
   ```

4. Create a second dynamic device group named `GRP-Intune-iOS-BYOD` with the rule:
   ```
   (device.operatingSystem -eq "iOS") and (device.deviceOwnership -eq "Personal")
   ```

5. Create a third group named `GRP-Intune-Win-Ring1-Pilot` as an **Assigned** group. Manually add 5–10 test device or user accounts as members to simulate a pilot ring.

6. Create a fourth dynamic user group named `GRP-Intune-Users-Finance` with the rule:
   ```
   (user.department -eq "Finance")
   ```

7. Navigate to **Intune** > **Devices** > **Device categories** and create three categories: `Corporate-Standard`, `Kiosk`, and `Executive`.

8. Return to each dynamic group, click **Validate rules**, and test with a sample device or user object. Confirm the evaluation result is as expected.

9. Wait up to 5 minutes for dynamic group membership to populate, then click each group and verify **Members** are present and correct.

10. In your team wiki or SharePoint, create a naming convention table documenting all Platform tokens, PolicyType tokens, Description guidelines, and Scope tokens with at least one example for each. Share the document link with the team.

**Expected Outcome:** You have at least four Azure AD groups (two dynamic device, one dynamic user, one assigned pilot ring), three device categories configured, and a published naming convention document ready for the team.

---

### Lab 10.2: Build a Phased Deployment Strategy

**Objective:** Define pilot and broad deployment rings, create corresponding Windows Update ring profiles, and configure a phased app assignment with promotion criteria.

**Steps:**

1. In the Intune portal, navigate to **Devices** > **Windows** > **Update rings for Windows 10 and later**. Click **+ Create profile**.

2. Name the profile `WIN-UPD-Quality-Ring0-IT`. Set **Quality update deferral period** to `0` days and **Automatic update behaviour** to `Auto install and restart at scheduled time`. Assign to `GRP-Intune-Win-Ring0-IT`. Save.

3. Create a second update ring named `WIN-UPD-Quality-Ring1-Pilot`. Set **Quality update deferral period** to `7` days. Assign to `GRP-Intune-Win-Ring1-Pilot`. Save.

4. Create a third update ring named `WIN-UPD-Quality-Ring2-Broad`. Set **Quality update deferral period** to `14` days. Assign to `GRP-Intune-Win-Ring2-Broad` (exclude Ring 0 and Ring 1 groups). Save.

5. Navigate to **Apps** > **Windows** and select an existing app (or add Microsoft To Do from the Microsoft Store). Click **Properties** > **Assignments** > **+ Add group**.

6. Add the first assignment: Group = `GRP-Intune-Win-Ring0-IT`, Intent = **Required**, no scheduled deadline. Save.

7. Simulate Ring 0 sign-off by checking **Device install status** for the app. Confirm it reports Installed for Ring 0 devices before proceeding.

8. Edit assignments to also include `GRP-Intune-Win-Ring1-Pilot` with intent **Required** and a **deadline** set 7 days from today. Save.

9. Document your phased rollout plan: who approves the promotion from Ring 0 → Ring 1 → Ring 2, what success metrics indicate readiness (for example, less than 2% install failure rate), and what the rollback procedure is if issues are found.

**Expected Outcome:** Three Windows Update rings are configured with staggered deferral periods, an app is staged for ring-based rollout with a phased assignment, and a promotion criteria document exists for the team to follow.

---

### Lab 10.3: Troubleshoot an Application Installation Failure

**Objective:** Use Windows Event Viewer, Intune Management Extension (IME) logs, and the Intune portal diagnostic tools to identify and resolve a simulated application installation failure.

**Steps:**

1. In **Intune** > **Apps** > **Windows**, create a Win32 app with an intentionally incorrect detection rule (for example, check for a registry key path that will not exist after the app installs) to simulate a failure. Package any small executable with `IntuneWinAppUtil.exe` and upload it.

2. Assign the app as **Required** to a test device in `GRP-Intune-Win-Ring0-IT`. Wait 15–20 minutes for the Intune policy cycle, or force a sync on the device via **Settings** > **Accounts** > **Access work or school** > **Info** > **Sync**.

3. Open the Intune portal. Navigate to **Apps** > select the app > **Device install status**. Find the test device and note the **Status** and **Error code** displayed.

4. On the Windows test device, open **Event Viewer** and navigate to: **Applications and Services Logs** > **Microsoft** > **Windows** > **DeviceManagement-Enterprise-Diagnostics-Provider** > **Admin**. Filter for Error-level events and look for any referencing the app or the MDM channel.

5. On the device, navigate to `C:\ProgramData\Microsoft\IntuneManagementExtension\Logs\` and open `IntuneManagementExtension.log` in a text editor (or CMTrace / OneTrace for colour-coded viewing).

6. Search the log for the application name and for the string `[Win32App]`. Review entries showing the installer exit code and the detection rule evaluation result.

7. Common error codes to cross-reference:
   - `0x87D1041C` — app not detected after install (detection rule mismatch)
   - `0x80070005` — access denied (permissions issue)
   - `0x80070002` — file not found (bad install source path)
   - `0x8007007b` — invalid file name syntax in command line

8. Return to the Intune portal and select the test device. Click **Device actions** > **Collect diagnostics**. After collection completes, download the ZIP archive and examine the `IntuneManagementExtension*.log` files inside to compare with your on-device findings.

9. Correct the detection rule to a file or registry path that will genuinely exist after a successful install. Edit the app in the portal and update the detection rule. Sync the device again.

10. Confirm the app now shows **Installed** under Device install status in the portal and that it appears installed on the device. Document the troubleshooting steps, root cause, and resolution as a runbook entry for future reference.

**Expected Outcome:** You have walked through the full IME log analysis workflow, used the Collect Diagnostics feature, identified the root cause of the installation failure, resolved it, and documented a reusable troubleshooting runbook.

---

### Lab 10.4: Review and Audit Security Policies

**Objective:** Generate a compliance report from the Intune portal, identify policy gaps against a security baseline, review audit logs, and produce a written remediation plan.

**Steps:**

1. In the Intune portal, navigate to **Reports** > **Device compliance** > **Compliance report (Organisational)**. Click **Generate report** and wait for it to complete.

2. Click **Export** to download the report as a CSV file. Open it and identify the top three compliance failure reasons (for example, BitLocker not enabled, OS version out of date, no antivirus detected).

3. Navigate to **Endpoint security** > **Security baselines** > select your deployed baseline (for example, the Microsoft Defender for Endpoint Baseline). Click your profile and then click **Device status** to review devices showing Succeeded, Error, or Conflict.

4. Click **Per-setting status** within the baseline profile. Identify any settings where more than 10% of devices report an error or conflict. Note these for your remediation plan.

5. Navigate to **Devices** > **Monitor** > **Policy compliance**. Sort by compliance percentage and identify any policy below 90% compliance. Click into one of those policies and review which devices are failing.

6. For one noncompliant device, navigate to its detail page. Review the **Device configuration** section and look for profiles in **Error** or **Conflict** state. Note the specific conflicting settings.

7. Navigate to **Troubleshoot + support** > **Troubleshoot** and enter a user UPN. Review the full list of policies, apps, and compliance state assigned to that user and their devices. Note any discrepancies.

8. Navigate to **Tenant administration** > **Audit logs**. Filter by **Date range** (last 7 days) and **Category** = `DeviceConfiguration`. Review what changes were made and by whom. Export the log as CSV for your records.

9. If any configuration profiles show **Conflict** status, navigate to the overlapping profiles and remove the duplicate setting from one of them. Confirm the conflict state clears within 15 minutes after the device next syncs.

10. Compile a remediation plan document with the following columns: **Finding** (the specific gap or failure), **Risk Level** (High, Medium, or Low), and **Remediation Action** (specific Intune action to take, owner, and due date). Populate it with at least five findings from the steps above and present it as if it were a delivery to a security team.

**Expected Outcome:** You have a CSV export of noncompliant devices, identified the top compliance and baseline gaps, reviewed recent audit log activity, resolved at least one policy conflict, and produced a structured remediation plan with risk levels and assigned owners.

---

## Best Practices

### Do's

- **Use a naming convention from Day 1** — retrofitting naming to hundreds of existing policies is exponentially harder than starting correctly
- **Always pilot before broad deployment** — even simple configuration profiles can break things at scale in unexpected ways
- **Enable and retain audit logs** — ship to Log Analytics for long-term retention beyond the 30-day portal default
- **Use Scope Tags** to segment admin access by region, department, or platform
- **Set Description fields** on every policy, app, and group with owner, ticket reference, and review date
- **Test Win32 apps on a clean VM** before uploading to Intune — what works on your admin machine may fail on a freshly imaged device
- **Use Conditional Access to enforce compliance** — compliance without CA enforcement is decorative, not protective
- **Review group memberships quarterly** — dynamic rules drift as attribute data changes in HR systems
- **Document your break-glass procedure** before you need it, not after an incident
- **Use Enrollment Status Page (ESP)** for Autopilot to ensure all required policies and apps apply before the user lands on the desktop
- **Version-control your configuration exports** using Intune's backup capability or community tools such as IntuneCD

### Don'ts

- **Never target "All Devices" for a new policy without first validating in a pilot ring** — one bad setting can affect thousands of endpoints simultaneously
- **Do not share admin accounts** — every administrator needs individual accountability in audit logs
- **Do not mix compliance and configuration settings in the same profile** — keep each profile focused on one concern
- **Do not delete policies that are still assigned** — unassign first, wait for devices to receive the removal, then delete
- **Do not rely on user-driven enrollment for kiosk or shared devices** — use Autopilot or pre-provisioning
- **Do not leave enrollment restrictions at their default (allow all)** without defining which platforms and OS versions you support
- **Do not ignore the "Policy not applicable" state** — it often means your group targeting or platform filter is wrong
- **Do not manage feature updates and quality updates in the same ring profile** — keep them separate for independent deferral control
- **Do not use PowerShell scripts for settings that have a native Intune configuration profile** — scripts are harder to audit and their status is less visible in reporting
- **Do not configure Intune without a defined offboarding process** — devices that leave the tenant without being wiped are a data security risk

---

## Common Issues and Troubleshooting

| Issue | Cause | Solution |
|---|---|---|
| Policy shows "Pending" indefinitely | Device has not checked in; MDM channel issue | Force sync via Company Portal or Settings > Accounts > Sync; verify device enrollment status |
| App stuck in "Installing" | IME service crashed or a restart is pending | Restart the Microsoft Intune Management Extension service; review IntuneManagementExtension.log |
| Compliance policy shows "Not applicable" | Device platform does not match policy target or device is not enrolled | Verify device platform and enrollment state; check group membership |
| "Conflict" state on configuration profile | Two profiles set the same CSP/setting with different values | Use the Conflict report to identify both profiles; remove the duplicate setting from one |
| BitLocker compliance fails on Azure AD-joined devices | TPM not ready at enrollment or escrow policy did not apply in time | Deploy BitLocker profile before first user logon using ESP; verify escrow to Azure AD is enabled |
| Win32 app detection rule never matches | Detection checks a file or registry path that does not exist after install | Test the detection rule locally with PowerShell before encoding; verify path capitalisation |
| Autopilot device stuck at ESP | A Required app is failing to install during provisioning | Check app install status in ESP; review IME logs; ensure app is not blocked by an AV policy exclusion issue |
| Dynamic group not populating | Attribute referenced in rule is null or not synced from on-premises AD | Verify the attribute value in user/device properties in Entra ID; check Azure AD Connect sync scope |
| SCEP certificate not deploying | NDES/SCEP connector offline or certificate template mismatch | Check Intune Connector service health; review NDES event logs; validate template OID configuration |
| Devices randomly going non-compliant | Compliance policy grace period expired or NTP drift causing time-based check failures | Review compliance policy grace period setting; ensure devices sync time via domain or internet NTP |
| Script fails silently | Script runs in 32-bit context by default on most devices | Enable "Run script in 64-bit PowerShell host" in the script assignment settings |
| App Protection Policy not applied | User is not licensed for Intune App Protection or app does not support the Intune SDK | Verify the Intune license assignment on the user; confirm the app supports the Intune SDK or App Wrapping Tool |

---

## Assessment Questions

**Question 1:** You deploy a new compliance policy to "All Devices" and 400 devices immediately go non-compliant, blocking access via Conditional Access. What should you have done to prevent this?

<details>
<summary>Answer</summary>

You should have used a phased ring-based deployment. By first targeting Ring 0 (IT staff, approximately 20 devices), you could validate the policy had no unintended consequences before expanding to Ring 1 (Pilot, 5–10%) and then the full fleet. Additionally, setting a non-compliance action grace period of 24–72 hours before access is blocked provides a buffer for devices to check in and remediate. Never target "All Devices" for a new compliance policy without prior ring validation, and always test the interaction with Conditional Access before enabling enforcement.

</details>

---

**Question 2:** An administrator reports that a Win32 application shows "Install Failed" on 15 devices. What are the first three sources you would check to diagnose the issue?

<details>
<summary>Answer</summary>

1. **Intune portal — Device install status:** Navigate to Apps > select the app > Device install status, then click the failed device to see the raw error code and last sync time.
2. **IME log on the device:** Open `C:\ProgramData\Microsoft\IntuneManagementExtension\Logs\IntuneManagementExtension.log` and search for the app name and `[Win32App]` entries, which show the installer exit code and detection rule evaluation result.
3. **Windows Event Viewer:** Navigate to Applications and Services Logs > Microsoft > Windows > DeviceManagement-Enterprise-Diagnostics-Provider > Admin and look for Error-level events referencing the app or MDM channel.

If these do not resolve the issue, use the Collect Diagnostics device action in the portal to retrieve a full diagnostic bundle including all IME logs.

</details>

---

**Question 3:** Your tenant has grown to 350 Intune policies with no naming convention. What is your recommended approach to remediate this without disrupting production devices?

<details>
<summary>Answer</summary>

A safe remediation approach has five steps. First, export the current policy inventory using Intune's export or a community tool such as IntuneCD to get a complete list. Second, categorise policies by platform and type in a spreadsheet and identify duplicates and orphaned (unassigned) policies. Third, delete unassigned and orphaned policies first — they carry no enforcement risk. Fourth, rename surviving policies using the new convention; renaming a policy in Intune does not cause a re-evaluation or a gap in enforcement, so it is safe to perform at any time. Fifth, consolidate genuine duplicates carefully: identify the canonical policy, verify both have identical settings, remove assignments from the duplicate, wait for devices to confirm the change, then delete the duplicate. Throughout, update the Description field on all surviving policies with owner, ticket reference, and review date.

</details>

---

**Question 4:** A new IT administrator needs to retire and wipe devices and reset passcodes but must not be able to create or modify policies. Which RBAC role would you assign?

<details>
<summary>Answer</summary>

The built-in Help Desk Operator role is the closest match — it grants the ability to perform remote actions (retire, wipe, reset passcode, sync) without permission to create or edit configuration or compliance policies. If you need tighter control, create a custom role with only these specific permissions enabled: Remote tasks: Retire, Remote tasks: Wipe, Remote tasks: Reset passcode, Remote tasks: Sync device, and Devices: Read. Pair the role with a Scope Tag to ensure the administrator can only act on devices within their responsible scope (for example, a specific region or department), preventing any lateral actions on devices they should not manage.

</details>

---

**Question 5:** What is the difference between a compliance policy grace period and a Conditional Access grant control, and why do you need both?

<details>
<summary>Answer</summary>

A compliance policy grace period is a setting within Intune's compliance policy that defines how many days a non-compliant device has before Intune marks it as non-compliant in its enforcement state. During the grace period, the device is still treated as compliant for access enforcement purposes. This gives newly enrolled devices time to receive all required settings and remediate (for example, to encrypt the disk with BitLocker) before being flagged. A Conditional Access grant control is a rule in Entra ID that requires the device to be marked compliant by Intune before it is granted access to a specific resource such as Exchange Online or SharePoint. Together they create a policy that is both operationally safe (the grace period prevents enrollment-day lockouts) and genuinely enforced (Conditional Access blocks access once the grace period expires and compliance has not been achieved). Without the grace period, every newly enrolled device is immediately blocked. Without Conditional Access, the compliance state is informational only and non-compliant devices continue to access resources unchecked.

</details>

---

## Key Resources

- [Microsoft Intune documentation overview](https://learn.microsoft.com/en-us/mem/intune/)
- [Intune planning guide](https://learn.microsoft.com/en-us/mem/intune/fundamentals/intune-planning-guide)
- [Role-based access control (RBAC) with Intune](https://learn.microsoft.com/en-us/mem/intune/fundamentals/role-based-access-control)
- [Scope tags for distributed IT](https://learn.microsoft.com/en-us/mem/intune/fundamentals/scope-tags)
- [Windows Update rings in Intune](https://learn.microsoft.com/en-us/mem/intune/protect/windows-10-update-rings)
- [Intune Win32 app management](https://learn.microsoft.com/en-us/mem/intune/apps/apps-win32-app-management)
- [Troubleshoot Win32 app installation issues](https://learn.microsoft.com/en-us/mem/intune/apps/troubleshoot-app-install)
- [Intune audit logs](https://learn.microsoft.com/en-us/mem/intune/fundamentals/monitor-audit-logs)
- [Device compliance and Conditional Access](https://learn.microsoft.com/en-us/mem/intune/protect/device-compliance-get-started)
- [Azure AD dynamic group membership rules](https://learn.microsoft.com/en-us/azure/active-directory/enterprise-users/groups-dynamic-membership)
- [Intune security baselines](https://learn.microsoft.com/en-us/mem/intune/protect/security-baselines)
- [Microsoft Intune Tech Community](https://techcommunity.microsoft.com/t5/microsoft-intune/ct-p/MicrosoftIntune)
- [IntuneCD — backup and document your Intune tenant](https://github.com/almenscorner/IntuneCD)

---

## Next Steps

Congratulations on completing Module 10. You have covered the operational best practices that separate a well-run Intune deployment from an unmanageable one.

**Immediate actions (this week):**
- Audit your current group and policy names against the naming convention and begin renaming
- Review your admin role assignments and remove any over-privileged accounts
- Enable Audit Log shipping to Log Analytics if not already configured

**Short-term (next 30 days):**
- Implement deployment rings for your next application or update rollout
- Create or update your enrollment and app packaging runbooks
- Schedule a quarterly policy hygiene review in your team calendar

**Proceed to Module 11 — Capstone Project:**
In the capstone, you will design and deploy a complete Intune environment from scratch, applying naming conventions, group design, phased rollout, security baselines, and compliance reporting in an integrated end-to-end scenario that mirrors a real enterprise deployment.

---

*Module 10 of 11 — Microsoft Intune Endpoint Administration Course*

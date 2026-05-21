# Module 07: Remote Help & Support

## Module Overview

Remote Help is a cloud-based solution integrated into Microsoft Intune that enables IT administrators and support staff to provide real-time remote assistance to end users on managed Windows devices. Unlike traditional remote desktop tools, Remote Help is fully integrated with Azure Active Directory and Intune Role-Based Access Control (RBAC), ensuring that only authorized personnel can connect to corporate devices. This module covers everything from licensing and configuration to hands-on troubleshooting workflows that are essential for enterprise IT support teams.

By the end of this module, you will understand how to deploy and manage Remote Help across your organization, how to perform live remote assistance sessions, and how to diagnose and resolve the most common device enrollment and management issues using Intune's built-in diagnostic tools.

---

## Learning Objectives

By completing this module, you will be able to:

- Describe what Remote Help is and how it integrates with Microsoft Intune
- Identify the licensing requirements needed to use Remote Help in production
- Configure tenant-level Remote Help settings in the Intune admin center
- Understand the difference between Helper and Sharer roles in a Remote Help session
- Compare Remote Help with other remote assistance tools (Quick Assist, TeamViewer)
- Assign and manage RBAC permissions for Remote Help scenarios
- Initiate and manage a Remote Help session from start to finish
- Collect diagnostic logs and device sync information to troubleshoot managed devices
- Identify and resolve common Intune enrollment errors
- Apply best practices for secure and compliant remote support operations

---

## Key Topics

### 1. What is Remote Help in Intune

Remote Help is a premium add-on feature for Microsoft Intune that provides a secure, cloud-brokered remote assistance experience. It is built into the Company Portal ecosystem and uses Azure AD authentication to verify both the helper (IT support) and the sharer (end user) before establishing a connection.

**Key capabilities include:**

- **Full control and view-only modes** — Helpers can either observe the user's screen or take full interactive control, depending on permissions granted
- **Elevation support** — Helpers with appropriate permissions can perform administrative actions on standard user accounts without requiring the user to have local admin rights
- **Chat during session** — Built-in text chat allows communication without needing a separate tool
- **Session audit logs** — All Remote Help sessions are logged in Intune for compliance and auditing purposes
- **Conditional Access support** — Remote Help respects your Conditional Access policies, preventing connections from non-compliant or unmanaged devices

Remote Help is currently supported on:

| Platform | Support Level |
|---|---|
| Windows 10 (1909+) | Full support |
| Windows 11 | Full support |
| macOS | Limited (view-only, preview) |
| Android | Not supported |
| iOS/iPadOS | Not supported |

---

### 2. Licensing Requirements

Remote Help is not included in the base Microsoft Intune license. It requires one of the following:

| License / Plan | Remote Help Included |
|---|---|
| Microsoft Intune Plan 1 (standalone) | ❌ No — requires add-on |
| Microsoft Intune Plan 2 | ✅ Yes — included |
| Microsoft Intune Suite | ✅ Yes — included |
| Microsoft 365 E3 + Intune add-on | ✅ Yes — with add-on |
| Microsoft 365 E5 | ✅ Yes — included |
| Microsoft 365 Business Premium | ❌ No — requires upgrade |

**Important licensing notes:**

- The **helper** (IT support staff) must have a license that includes Remote Help assigned to their account
- The **sharer** (end user) does NOT need a Remote Help license — only a standard Intune license is required
- You can purchase the Remote Help add-on separately if you are on Intune Plan 1
- Trial tenants can test Remote Help features during the trial period

To verify your licensing:
1. Navigate to **Microsoft Intune admin center** → **Tenant administration** → **Tenant status**
2. Review the **Connector status** and **Service details** sections
3. Confirm Remote Help is shown as an active feature

---

### 3. Roles and Permissions (Helper vs. Sharer)

Remote Help uses a two-party model where one person provides help and another receives it.

#### Helper Role

The **Helper** is the IT support technician or administrator who initiates or accepts a remote connection. The helper:

- Must be assigned an Intune role that includes Remote Help permissions
- Sees a 8-character session code that the sharer must enter
- Can request to view the screen or take full control (subject to permissions)
- Can optionally elevate to perform admin actions (requires "Elevation" permission in RBAC)

**Built-in roles with Remote Help access:**

| Role | View Screen | Full Control | Elevation |
|---|---|---|---|
| Help Desk Operator | ✅ | ✅ | ❌ |
| Intune Administrator | ✅ | ✅ | ✅ |
| Custom Role (configured) | Configurable | Configurable | Configurable |

#### Sharer Role

The **Sharer** is the end user requesting assistance. The sharer:

- Does NOT need an Intune admin role
- Receives a session code from the helper or generates one through the Company Portal
- Must consent to screen sharing and control before the session begins
- Can end the session at any time by closing the Remote Help window
- Is notified at all times whether the helper is in view-only or full-control mode

#### RBAC Permission Reference

Remote Help permissions are found under **Intune RBAC** → **Remote Help app**:

| Permission | Description |
|---|---|
| View screen | Allow helper to see the sharer's screen |
| Take full control | Allow helper to interact with mouse/keyboard |
| Elevation | Allow helper to run elevated actions on behalf of the user |
| View reports | Allow helper to review session audit logs |

---

### 4. Remote Help vs. Quick Assist vs. TeamViewer

Understanding when to use each tool is important for selecting the right support approach.

| Feature | Remote Help (Intune) | Quick Assist (Windows) | TeamViewer |
|---|---|---|---|
| Integration with Intune | ✅ Native | ❌ None | ✅ Connector available |
| Azure AD authentication | ✅ Required | ❌ Microsoft Account only | ❌ No |
| Audit logging in Intune | ✅ Full logs | ❌ None | ⚠️ Partial (via connector) |
| Elevation support | ✅ Yes | ⚠️ Limited | ✅ Yes |
| Works on unmanaged devices | ❌ No | ✅ Yes | ✅ Yes |
| License required | ✅ Yes (add-on/plan) | ✅ Included in Windows | 💰 Paid license |
| RBAC enforcement | ✅ Yes | ❌ No | ⚠️ Limited |
| Conditional Access support | ✅ Yes | ❌ No | ❌ No |
| Cross-platform | Windows only* | Windows only | Multi-platform |

**Recommendation:** Use Remote Help for all Intune-managed Windows devices in enterprise scenarios where audit trails, RBAC, and Conditional Access compliance are required. Use Quick Assist only for ad hoc support on unmanaged devices or personal machines.

---

### 5. Configuring Remote Help Tenant Settings

Before users can use Remote Help, you must enable and configure it at the tenant level.

**To enable Remote Help in the Intune admin center:**

1. Sign in to the [Microsoft Intune admin center](https://intune.microsoft.com)
2. Navigate to **Tenant administration** → **Remote Help**
3. On the **Settings** tab, set **Enable Remote Help** to **Enabled**
4. Configure the following options:

| Setting | Description | Recommendation |
|---|---|---|
| Enable Remote Help | Master toggle to activate the feature | Enabled |
| Allow Remote Help to unenrolled devices | Permit connections to devices not enrolled in Intune | Disabled (security risk) |
| Disable chat | Remove the in-session text chat function | Per policy |
| Enable elevation | Allow helpers to run elevated actions | Enabled for Help Desk role |

5. Click **Save**
6. Deploy the **Remote Help application** to target devices via Intune app deployment

**To deploy the Remote Help app:**

1. Navigate to **Apps** → **Windows** → **Add**
2. Select **Microsoft app** as the app type
3. Search for **Remote Help** and select it
4. Assign the app to the appropriate device or user groups
5. The app can also be downloaded manually from [https://aka.ms/downloadremotehelp](https://aka.ms/downloadremotehelp)

---

### 6. Troubleshooting Managed Devices

When Remote Help alone cannot resolve an issue, Intune provides built-in diagnostic and troubleshooting capabilities.

#### MDM Diagnostic Logs

**From the device (Windows):**

```
Windows Settings → Accounts → Access work or school → [Account] → Info → Create report
```

This generates an `MDMDiagReport.html` and associated logs in:
```
C:\Users\Public\Documents\MDMDiagnostics\
```

**From Intune admin center:**

1. Navigate to **Devices** → **Windows** → select the device
2. Click **...** (ellipsis) → **Collect diagnostics**
3. Intune pushes a request to the device; logs are uploaded when the device checks in
4. Download the collected logs from **Device diagnostics** tab

#### Device Sync

Force a policy sync from the admin center:

1. Select the device in **Devices** → **Windows**
2. Click **Sync** in the top action bar
3. The device will check in within 5–15 minutes (or immediately if online)

You can also trigger sync from the device:
- **Settings** → **Accounts** → **Access work or school** → **[Account]** → **Info** → **Sync**

#### Remote Actions Available from Intune

| Action | Description |
|---|---|
| Sync | Force device to check in for policy updates |
| Restart | Remotely reboot the device |
| Collect diagnostics | Gather MDM logs remotely |
| Fresh Start | Reinstall Windows while retaining user data |
| Autopilot Reset | Reset to out-of-box state without reimaging |
| Retire | Remove company data (MDM unenroll) |
| Wipe | Factory reset the device |

---

### 7. Common Enrollment Issues

| Issue | Likely Cause | Resolution |
|---|---|---|
| "MDM enrollment failed" error | User not licensed for Intune | Assign Intune license to the user |
| Device shows as "Not compliant" after enrollment | Compliance policy not yet evaluated | Wait 10–15 min; force sync if needed |
| "Your organization's policies are preventing enrollment" | Enrollment restrictions blocking the device type or OS version | Review **Enrollment restrictions** in Intune |
| Duplicate device records | Device re-enrolled without cleaning old record | Delete stale device record from Intune |
| Hybrid Azure AD join not completing | ADFS or sync issues | Verify Azure AD Connect sync and ADFS health |
| Autopilot device not found | Hardware hash not uploaded | Upload hardware hash via CSV or re-run Get-WindowsAutoPilotInfo |
| Company Portal shows "Setup not complete" | Missing required apps or policies still deploying | Allow 30–60 minutes for initial deployment to complete |

---

## Hands-On Labs

### Lab 7.1: Install and Enable Remote Help

**Objective:** Enable Remote Help at the tenant level and deploy the Remote Help application to a group of Windows devices.

**Prerequisites:**
- Intune Administrator role
- A license that includes Remote Help (Intune Plan 2, Intune Suite, or add-on)
- A test device group in Intune

**Steps:**

1. Sign in to the [Microsoft Intune admin center](https://intune.microsoft.com) with Global Administrator or Intune Administrator credentials.

2. In the left navigation menu, click **Tenant administration**, then click **Remote Help** under the **Remote Help** section.

3. On the **Settings** tab, locate the **Enable Remote Help** toggle and set it to **Enabled**.

4. Review the **Allow Remote Help to unenrolled devices** option. For this lab, ensure it is set to **Disabled** to restrict Remote Help to managed devices only.

5. Under **Disable chat**, leave it set to **Not configured** (chat enabled) for the lab environment.

6. Click **Save** at the top of the page to apply the tenant settings.

7. Navigate to **Apps** → **Windows** in the left menu.

8. Click **+ Add** in the top action bar.

9. In the **Select app type** pane, scroll to find **Microsoft app** under the **Other** section, then click **Select**.

10. In the app search box, type **Remote Help** and select the **Remote Help** application from the results.

11. Click **Next** through the **App information** page (defaults are pre-filled by Microsoft).

12. On the **Assignments** tab, click **+ Add group** under **Required** and select your test Windows device group.

13. Click **Next**, review the summary, then click **Create**.

14. Verify the app deployment by navigating to **Devices** → **Windows** → select a test device → **Apps** and confirm Remote Help appears in the installed apps list after the device syncs (allow 15–30 minutes).

**Expected Outcome:** Remote Help is enabled in the tenant and the Remote Help application is deployed to your test device group. The Remote Help app appears installed on target devices.

---

### Lab 7.2: Configure Roles and Permissions (RBAC)

**Objective:** Create a custom Intune RBAC role with specific Remote Help permissions and assign it to a help desk security group.

**Prerequisites:**
- Intune Administrator or Global Administrator role
- A security group representing your help desk team (e.g., `SG-HelpDesk-Tier1`)
- A scope group containing the devices/users the help desk will support

**Steps:**

1. Sign in to the [Microsoft Intune admin center](https://intune.microsoft.com).

2. Navigate to **Tenant administration** → **Roles** → **All roles**.

3. Click **+ Create** to create a new custom role.

4. On the **Basics** tab, enter the following:
   - **Name:** `Help Desk - Remote Help Tier 1`
   - **Description:** `Allows Tier 1 help desk staff to view and control end-user screens using Remote Help. No elevation permitted.`

5. Click **Next** to proceed to the **Permissions** tab.

6. In the permissions list, scroll to find the **Remote Help app** section and expand it.

7. Enable the following permissions:
   - ✅ **View screen** — set to **Yes**
   - ✅ **Take full control** — set to **Yes**
   - ❌ **Elevation** — leave set to **No** (Tier 1 should not have elevation rights)

8. Optionally expand the **Remote tasks** section and enable **Collect diagnostics** and **Sync** to allow Tier 1 helpers to perform basic troubleshooting actions.

9. Click **Next** to proceed to the **Scope (Tags)** tab. Leave scope tags as default for this lab, then click **Next**.

10. On the **Review + create** tab, verify all settings, then click **Create**.

11. From the **All roles** list, click the newly created role **Help Desk - Remote Help Tier 1**.

12. Click **Assignments** → **+ Assign**.

13. On the **Basics** tab of the assignment:
    - **Assignment name:** `HelpDesk-Tier1-Assignment`
    - **Members:** Select your help desk security group (`SG-HelpDesk-Tier1`)

14. On the **Scope** tab, select the scope group representing the end users and devices this role should cover.

15. Click **Next**, review, and click **Create**.

16. Verify the assignment by signing in as a member of `SG-HelpDesk-Tier1` and navigating to the Intune admin center. Confirm that only the permitted actions are available.

**Expected Outcome:** A custom RBAC role with Remote Help view and control permissions (but no elevation) is created and assigned to the help desk group. Members of that group can now use Remote Help within their assigned scope.

---

### Lab 7.3: Perform a Remote Assistance Session

**Objective:** Initiate a Remote Help session between a helper (IT support) and a sharer (end user) using the Remote Help application and session code.

**Prerequisites:**
- Remote Help enabled in tenant (Lab 7.1 complete)
- Remote Help app installed on the end-user device
- Helper has an RBAC role with Remote Help permissions (Lab 7.2 complete)
- Both helper and sharer are signed in with their Azure AD credentials

**Steps:**

1. **On the Helper's machine (IT support technician):**
   Open the **Microsoft Intune admin center** and navigate to **Devices** → **Windows**.

2. Locate the target device and click on it to open the device details page.

3. In the device action bar at the top, click **New Remote Help session**. This will launch the Remote Help application (must be installed on the helper's device).

   > Alternatively, the helper can open the **Remote Help** application directly from the Start menu and sign in with their Azure AD credentials.

4. In the Remote Help app, click **Get a security code** (or **Help someone**). The app will generate an 8-character alphanumeric session code displayed on screen.

5. **Share the session code** with the end user via phone, Teams message, or email. The session code is valid for a limited time (approximately 10 minutes).

6. **On the Sharer's machine (end user):**
   Open the **Remote Help** application from the Start menu or Company Portal. Sign in with Azure AD credentials when prompted.

7. On the Remote Help welcome screen, click **Get help** and enter the 8-character session code provided by the helper.

8. The end user will be prompted to choose:
   - **View screen only** — helper can see but not interact
   - **Full control** — helper can use mouse and keyboard

   Select **Full control** for this lab and click **Share screen**.

9. **Back on the Helper's machine:**
   The helper will see a request notification. Accept the connection to begin the session.

10. Verify the session is active:
    - The sharer sees a **Remote Help** toolbar at the top of their screen indicating the session is live
    - The helper can view and control the sharer's desktop

11. Test the connection by opening **Notepad** on the sharer's device from the helper's interface.

12. When finished, the helper clicks **Leave** or the sharer clicks **Stop sharing** in the Remote Help toolbar to end the session.

13. Navigate to **Tenant administration** → **Remote Help** → **Session history** in the Intune admin center to verify the session was logged with timestamps, user names, and session duration.

**Expected Outcome:** A successful Remote Help session is established between the helper and sharer. The helper can view and control the sharer's screen. The session is recorded in Intune's audit logs with full details.

---

### Lab 7.4: Troubleshoot Enrollment Issues

**Objective:** Use Intune's built-in diagnostic tools, MDM logs, and remote actions to identify and resolve a simulated enrollment issue on a managed Windows device.

**Prerequisites:**
- A Windows device enrolled (or attempting to enroll) in Intune
- Intune Administrator or Help Desk role with Collect Diagnostics permission
- Access to the Intune admin center

**Steps:**

1. Sign in to the [Microsoft Intune admin center](https://intune.microsoft.com) and navigate to **Devices** → **Windows**.

2. Locate the device experiencing the enrollment or compliance issue. Note its **Enrollment status**, **Compliance status**, and **Last check-in** time.

3. Click on the device to open the **Device overview** pane. Review:
   - **Enrollment type** — is it correctly enrolled (e.g., Azure AD Joined, Hybrid Joined)?
   - **Compliance** — is it compliant or noncompliant, and which policies are failing?
   - **Device configuration** — are profiles assigned and showing success or error?

4. Click on the **Device configuration** tab to review each assigned profile. Identify any profiles showing **Error** or **Conflict** status. Note the profile name and error code.

5. Return to the device overview and click **Sync** in the top action bar to force the device to check in immediately and re-evaluate all policies.

6. After allowing 5–10 minutes for the sync to complete, refresh the device page and note whether compliance status has changed.

7. If the issue persists, click **...** (More) in the top action bar and select **Collect diagnostics**. Confirm the action when prompted.

8. Navigate to the **Device diagnostics** tab and wait for the diagnostic package to be uploaded (this may take 10–15 minutes if the device is online).

9. Once available, click **Download** to save the diagnostic ZIP file to your local machine. Extract the archive and review:
   - `MDMDiagReport.html` — primary diagnostic report with enrollment status
   - `EventLog-Microsoft-Windows-DeviceManagement-Enterprise-Diagnostics-Provider*.evtx` — MDM event logs
   - `PolicyLog.txt` — applied and failed policies

10. On the affected **device itself**, open **Event Viewer** → **Applications and Services Logs** → **Microsoft** → **Windows** → **DeviceManagement-Enterprise-Diagnostics-Provider** → **Admin**. Look for errors related to enrollment or policy application.

11. Cross-reference the error codes found in the logs with the [Intune enrollment troubleshooting guide](https://learn.microsoft.com/en-us/troubleshoot/mem/intune/device-enrollment/troubleshoot-device-enrollment-in-intune).

12. If the device has a duplicate record (enrolled twice), navigate to **Devices** → **Windows**, filter by device name, and delete the stale/older record. Then perform a fresh sync on the current record.

13. If an enrollment restriction is blocking the device, navigate to **Devices** → **Enrollment** → **Enrollment restrictions** and review the **Device type restrictions** and **Device limit restrictions** to ensure the device type and OS version are permitted.

14. Document your findings: record the error code, root cause, and resolution steps taken.

**Expected Outcome:** You can locate the source of an enrollment or compliance issue using Intune diagnostic logs, successfully download and interpret the diagnostic package, and apply the appropriate fix (sync, delete stale record, update restrictions). The device shows as compliant and fully enrolled after remediation.

---

## Best Practices

### Do's ✅

- ✅ **Always verify the helper's identity** before sharing a session code — use a trusted channel (Teams call, verified email) to share codes
- ✅ **Use scope groups** in RBAC assignments to limit help desk staff to only their assigned users and devices
- ✅ **Enable session logging** and periodically review Remote Help session history for unauthorized access patterns
- ✅ **Use view-only mode** when demonstrating something to a user; reserve full control for active troubleshooting
- ✅ **Train users** to verify the identity of the person requesting remote access before accepting a session
- ✅ **Collect diagnostics before troubleshooting** to establish a baseline and avoid trial-and-error fixes
- ✅ **Force a device sync** before collecting diagnostics to ensure the most current policy state is captured
- ✅ **Document all remote sessions** with a ticket number or case ID for audit and compliance tracking
- ✅ **Revoke elevated permissions** from help desk roles unless explicitly required for their support tier
- ✅ **Test Remote Help deployments** in a pilot group before rolling out org-wide

### Don'ts ❌

- ❌ **Never share session codes in public channels** (open Slack channels, shared email aliases, or unencrypted messaging)
- ❌ **Do not enable Remote Help for unenrolled devices** unless you have a specific, documented business requirement
- ❌ **Do not assign Intune Administrator role** to all help desk staff — create least-privilege custom roles instead
- ❌ **Do not perform Remote Help sessions without the user's knowledge and consent** — always get explicit consent before connecting
- ❌ **Do not skip log review** when an enrollment fix appears to work — confirm the root cause to prevent recurrence
- ❌ **Do not delete active device records** in Intune without confirming the device is no longer in use
- ❌ **Do not rely solely on Remote Help** for devices that may be offline — ensure out-of-band communication channels exist
- ❌ **Do not grant elevation permissions to Tier 1 help desk** — reserve elevation for senior/Tier 2 administrators only
- ❌ **Do not ignore duplicate device records** — they cause policy conflicts and compliance reporting inaccuracies
- ❌ **Do not use Quick Assist as a substitute for Remote Help** in enterprise managed environments where audit logging is required

---

## Common Issues and Troubleshooting

| Issue | Cause | Solution |
|---|---|---|
| Remote Help app does not launch | App not installed on helper's device | Deploy Remote Help app via Intune to the helper's device or download from [aka.ms/downloadremotehelp](https://aka.ms/downloadremotehelp) |
| "Remote Help is not enabled for your organization" | Tenant setting not enabled | Navigate to **Tenant administration** → **Remote Help** → **Settings** and toggle **Enable Remote Help** to **Enabled** |
| Session code expires before sharer enters it | Code was not used within ~10 minutes | Generate a new session code and share it promptly |
| Helper cannot see sharer's screen after connecting | Sharer only granted view-only permission | Ask sharer to upgrade to full control by clicking **Allow full control** in the toolbar |
| "You do not have permission to provide Remote Help" | Helper's RBAC role lacks Remote Help permissions | Review and update the custom role to include View Screen and Take Full Control permissions |
| Remote Help app crashes immediately on launch | Outdated app version or missing Visual C++ runtime | Update Remote Help via Intune or manually; install latest Visual C++ Redistributable |
| Collect Diagnostics action is greyed out | Device is offline or the helper lacks the **Collect diagnostics** remote task permission | Verify device is online; add Collect Diagnostics to the helper's RBAC role |
| Device shows "Pending" compliance after enrollment | Compliance policy not yet evaluated | Allow 15–30 minutes; force sync; verify compliance policies are assigned to the user/device group |
| MDM enrollment fails with error 80180026 | User-based enrollment blocked by enrollment restrictions | Check **Device type restrictions** and ensure Windows (MDM) enrollment is allowed for this user group |
| Duplicate device entries in Intune | Device re-enrolled without removing old record | Filter devices by name, compare last check-in dates, and delete the stale record |
| Hybrid Azure AD join device not appearing in Intune | Azure AD Connect sync issue or missing MDM URL in Group Policy | Verify Azure AD Connect sync health; check that the MDM enrollment GPO is applied correctly |
| "Company Portal needs to be updated" on enrollment | Outdated Company Portal version on device | Push latest Company Portal update via Intune or Windows Store; verify update ring settings |

---

## Assessment Questions

Test your understanding of Module 07 concepts.

---

**Question 1:** Which license is required for the **helper** (IT support staff) to use Remote Help, and does the **sharer** (end user) need the same license?

<details>
<summary>Answer</summary>

The **helper** must have a license that includes Remote Help — this requires either **Microsoft Intune Plan 2**, the **Microsoft Intune Suite**, or the standalone **Remote Help add-on** for Intune Plan 1. The **sharer (end user) does NOT need a Remote Help license** — they only need a standard Intune device management license. Only one side of the connection (the helper) requires the premium Remote Help entitlement.

</details>

---

**Question 2:** Your Tier 1 help desk staff are reporting that they can see the end user's screen during a Remote Help session but cannot interact with the mouse or keyboard. What is the most likely cause?

<details>
<summary>Answer</summary>

The most likely cause is that the **end user (sharer) only granted view-only permission** when accepting the Remote Help session — they selected "View screen only" instead of "Full control." The sharer can upgrade the permission during the session by clicking **Allow full control** in the Remote Help toolbar. Additionally, verify that the helper's RBAC role has the **Take full control** permission enabled — if not, update the custom role in Intune.

</details>

---

**Question 3:** How would you force an enrolled Windows device to immediately re-evaluate all Intune policies without physically accessing the device?

<details>
<summary>Answer</summary>

From the **Microsoft Intune admin center**, navigate to **Devices** → **Windows**, select the target device, and click the **Sync** action in the top action bar. This sends a push notification to the device requesting it check in with Intune immediately. The device will re-download and re-apply all assigned policies, compliance rules, and app assignments. If the device is online, it typically checks in within a few minutes. The user can also trigger a sync locally via **Settings** → **Accounts** → **Access work or school** → **Info** → **Sync**.

</details>

---

**Question 4:** An end user reports that their device keeps showing as "Not enrolled" in the Company Portal despite going through the enrollment process twice. After checking the Intune admin center, you find two device records with the same name. What steps should you take?

<details>
<summary>Answer</summary>

This is a **duplicate device record** issue. The steps to resolve it are:

1. In the Intune admin center, navigate to **Devices** → **Windows** and search for the device name
2. Compare the two records — check **Last check-in** time, **Enrollment date**, and **Compliance status**
3. The record with the **older Last check-in** is likely the stale/orphaned record
4. Delete the stale record by selecting it and clicking **Delete**
5. On the device, navigate to **Settings** → **Accounts** → **Access work or school**, remove the existing work account, and re-enroll the device
6. Force a **Sync** on the new record and verify that only one record appears in Intune with the correct compliance status

</details>

---

**Question 5:** What is the key security advantage of using Remote Help over Windows Quick Assist in an enterprise Intune environment?

<details>
<summary>Answer</summary>

Remote Help provides several critical security advantages over Quick Assist in an enterprise environment:

- **Azure AD authentication required** — both the helper and sharer must authenticate with corporate Azure AD credentials; Quick Assist uses personal Microsoft Accounts with no corporate identity verification
- **RBAC enforcement** — Remote Help respects Intune's Role-Based Access Control, ensuring only authorized support staff can connect; Quick Assist has no equivalent access control
- **Full audit logging in Intune** — every Remote Help session is recorded in Intune with user identities, timestamps, duration, and actions taken; Quick Assist has no centralized audit trail
- **Conditional Access compliance** — Remote Help respects Conditional Access policies, preventing connections from non-compliant devices; Quick Assist bypasses these controls
- **Scope-based restrictions** — RBAC scope groups ensure help desk staff can only assist users and devices within their assigned scope

</details>

---

## Key Resources

- [Remote Help overview — Microsoft Learn](https://learn.microsoft.com/en-us/mem/intune/fundamentals/remote-help)
- [Set up Remote Help for Microsoft Intune](https://learn.microsoft.com/en-us/mem/intune/fundamentals/remote-help-windows)
- [Intune RBAC — Role-Based Access Control](https://learn.microsoft.com/en-us/mem/intune/fundamentals/role-based-access-control)
- [Troubleshoot device enrollment in Intune](https://learn.microsoft.com/en-us/troubleshoot/mem/intune/device-enrollment/troubleshoot-device-enrollment-in-intune)
- [Collect diagnostics from a Windows device](https://learn.microsoft.com/en-us/mem/intune/remote-actions/collect-diagnostics)
- [MDM enrollment of Windows devices — Microsoft Learn](https://learn.microsoft.com/en-us/windows/client-management/mdm-enrollment-of-windows-devices)
- [Intune Remote Help licensing add-on](https://learn.microsoft.com/en-us/mem/intune/fundamentals/intune-add-ons)
- [Use Remote Help on macOS (preview)](https://learn.microsoft.com/en-us/mem/intune/fundamentals/remote-help-macos)
- [Monitor and audit Remote Help sessions](https://learn.microsoft.com/en-us/mem/intune/fundamentals/remote-help#monitoring-and-reports)
- [Intune remote actions for Windows devices](https://learn.microsoft.com/en-us/mem/intune/remote-actions/device-management)

---

## Next Steps

Congratulations on completing **Module 07: Remote Help & Support**! You now have the knowledge and hands-on experience to deploy, configure, and operate Remote Help in a Microsoft Intune environment, and to effectively troubleshoot device enrollment and compliance issues using Intune's diagnostic toolset.

**Continue your learning journey:**

- 📘 **[Module 08: Windows Update Management](../Module-08-Windows-Update-Management/README.md)** — Learn how to manage Windows Updates using Intune Update Rings and Windows Autopatch
- 📘 **[Module 09: Endpoint Security & Microsoft Defender](../Module-09-Endpoint-Security/README.md)** — Configure Endpoint Security policies, Microsoft Defender Antivirus, and Attack Surface Reduction rules
- 📘 **[Module 10: Reporting & Monitoring](../Module-10-Reporting-Monitoring/README.md)** — Master Intune's built-in reporting, Azure Monitor integration, and operational dashboards

**Recommended hands-on practice:**

1. Set up a Remote Help pilot with 5–10 users in a dedicated test group before production rollout
2. Review your existing help desk RBAC roles and audit whether elevation permissions are correctly scoped
3. Practice the full diagnostic collection workflow on a non-production device
4. Create a runbook or SOP document for your help desk team based on the troubleshooting steps in this module

> 💡 **Pro Tip:** Schedule a quarterly review of Remote Help session logs in the Intune admin center to identify support patterns, recurring issues, and opportunities to proactively address common problems through policy or user education.

# Module 06: Security & Compliance

> 🛡️ **Security & Compliance** — Protect endpoints, enforce policies, and maintain a strong security posture across your organization with Microsoft Intune.

---

## Module Overview

This module covers the security and compliance capabilities of Microsoft Intune. You'll learn how to enforce compliance policies, configure Conditional Access, enable BitLocker encryption, manage Windows Firewall rules, integrate Microsoft Defender for Endpoint, and apply Security Baselines. These features work together to ensure that only healthy, compliant devices can access corporate resources, and that endpoints remain protected against modern threats.

By the end of this module, you will have practical experience configuring each major security pillar within the Intune ecosystem and understand how they interact to form a comprehensive Zero Trust endpoint security strategy.

---

## Learning Objectives

By the end of this module, you will be able to:

- **Understand** the role of Conditional Access in enforcing Zero Trust security
- **Create** device compliance policies for Windows, iOS, Android, and macOS
- **Configure** Conditional Access policies that gate access based on compliance state
- **Enable** BitLocker encryption on Windows devices using Intune
- **Manage** Windows Firewall rules and profiles through endpoint security policies
- **Integrate** Microsoft Defender for Endpoint with Intune for advanced threat protection
- **Apply** Security Baselines to harden device configurations
- **Troubleshoot** common compliance and security policy issues

---

## Key Topics

### 1. Conditional Access Concepts

#### What Is Conditional Access?
Conditional Access is an Azure AD (Microsoft Entra ID) capability that acts as a policy engine, evaluating signals before granting access to resources. Intune provides the compliance signal — Conditional Access enforces the gate.

- **Signals evaluated**: User identity, device platform, device compliance state, IP location, sign-in risk
- **Controls enforced**: Allow, block, or require MFA; require compliant device; require approved app
- **Common use cases**:
  - Block access from non-enrolled or non-compliant devices
  - Require MFA from untrusted networks
  - Allow only approved client apps for Exchange Online

#### Named Locations and Risk-Based Policies
- Define trusted IP ranges as Named Locations
- Use Azure AD Identity Protection risk scores (sign-in risk, user risk) as signals
- Apply different requirements for corporate vs. personal devices
- Combine location conditions with device compliance for layered access control

#### Conditional Access Grant Controls
| Control | Description |
|---------|-------------|
| **Require MFA** | User must complete multi-factor authentication |
| **Require compliant device** | Device must be marked compliant in Intune |
| **Require Hybrid Azure AD join** | Device must be joined to on-premises AD and registered in Azure AD |
| **Require approved client app** | Access only allowed from approved apps (e.g., Outlook mobile) |
| **Require app protection policy** | App must have an Intune MAM policy applied |
| **Require password change** | Force password reset on high-risk sign-ins |

> **Note**: Grant controls can be combined with **AND** (all required) or **OR** (any one sufficient) logic.

#### Session Controls
Conditional Access also supports session controls that limit what users can do *after* access is granted:
- **Sign-in frequency**: Force re-authentication after a configurable time
- **Persistent browser session**: Control whether "Stay signed in" is allowed
- **App enforced restrictions**: Pass device compliance/managed state to SharePoint and Exchange to limit download or printing
- **Conditional Access App Control (MCAS)**: Route sessions through Microsoft Defender for Cloud Apps for real-time monitoring

#### Conditional Access and Intune
- Intune marks devices as **Compliant** or **Non-compliant** based on compliance policies
- Azure AD Conditional Access reads the compliance state in real time
- Devices must be enrolled in Intune before compliance state is published
- Always exclude **break-glass emergency access accounts** from all Conditional Access policies
- Use **What If** tool in Conditional Access to simulate policy evaluation before going live

---

### 2. Endpoint Security Features

#### Endpoint Security Node in Intune
The **Endpoint Security** node in the Intune admin center provides focused policy types for security-specific configurations, separate from Device Configuration profiles:

- **Antivirus** — Configure Microsoft Defender Antivirus settings (real-time protection, cloud-delivered protection, exclusions)
- **Disk Encryption** — BitLocker (Windows) and FileVault (macOS)
- **Firewall** — Windows Firewall and macOS firewall rules and custom port/application rules
- **Endpoint Detection & Response (EDR)** — Defender for Endpoint onboarding package deployment
- **Attack Surface Reduction (ASR)** — Reduce attack vectors on Windows devices (block Office macros, block obfuscated scripts, exploit protection)
- **Account Protection** — Windows Hello for Business, Local Administrator Password Solution (LAPS), credential guard

#### Attack Surface Reduction (ASR) Rules
ASR rules are a powerful set of controls that block specific behaviors commonly exploited by malware:

| ASR Rule | What It Blocks |
|----------|----------------|
| Block executable content from email client and webmail | Malicious attachments from email |
| Block Office apps from creating child processes | Macro-based attacks spawning cmd.exe/PowerShell |
| Block credential stealing from LSASS | Mimikatz-style credential dumping |
| Block untrusted and unsigned processes from USB | Malware auto-running from removable drives |
| Use advanced protection against ransomware | Ransomware behavioral patterns |

> ASR rules support three modes: **Audit** (log only), **Block** (enforce and log), and **Warn** (user can override with justification).

#### Security Tasks
When Defender for Endpoint detects a vulnerability or misconfiguration, it surfaces **Security Tasks** in Intune. Admins can review and remediate these tasks directly, closing the loop between detection and remediation.

- Navigate to **Intune → Endpoint Security → Security Tasks** to view open items
- Each task includes: severity, affected devices, recommended remediation, and a link to the MDE vulnerability details
- Mark tasks as **Complete** after remediation to close the loop in MDE

---

### 3. Compliance Policies

#### What Is a Compliance Policy?
A compliance policy defines the rules and settings that a device must satisfy to be considered compliant. Devices that do not meet these requirements are marked non-compliant and can be blocked from accessing resources via Conditional Access.

#### Compliance Policy Settings (Windows)
- **Device Health**: BitLocker required, Secure Boot required, Code Integrity required
- **Device Properties**: Minimum/maximum OS version, valid OS builds
- **Configuration Manager Compliance**: Require device to be compliant in MECM (co-management scenarios)
- **System Security**: Password required, password complexity, firewall enabled, antivirus required, antispyware required, encryption required
- **Microsoft Defender for Endpoint**: Require machine risk score at or below a threshold (None, Low, Medium, High)

#### Compliance Policy Settings (iOS/Android)
- Require device encryption
- Jailbreak/root detection (blocks rooted/jailbroken devices)
- Minimum OS version
- Required apps installed
- App protection policy compliance
- Require device to be at or under a threat level (MDE integration for Android)
- Google Play Protect required (Android)
- SafetyNet attestation (Android) / DeviceCheck attestation (iOS)

#### Compliance Grace Periods and Actions
- Set a **grace period** before marking a device non-compliant (e.g., 3 days)
- Configure **actions for non-compliance**: send notification email, remotely lock device, retire device
- Use **scheduled actions** to escalate over time (e.g., notify on day 1, lock on day 7, retire on day 30)

#### Compliance Policy Workflow
1. Create compliance policy with required settings
2. Assign policy to device or user groups
3. Device checks in and evaluates policy
4. Intune reports compliance state to Azure AD
5. Conditional Access evaluates state and enforces access controls

#### Compliance Status Definitions

| Status | Meaning |
|--------|---------|
| **Compliant** | All settings are met; device can pass Conditional Access checks |
| **Not compliant** | One or more settings are not met; access may be blocked |
| **Not evaluated** | Policy has not yet been evaluated (device has not checked in) |
| **In grace period** | Device is non-compliant but within the grace period window |
| **Error** | Compliance policy could not be evaluated due to an error |

> 💡 **Tip**: Use the **Device compliance → Monitor → Setting compliance** report to see which specific settings are failing across your device fleet.

---

### 4. BitLocker Encryption

#### Why BitLocker?
BitLocker Drive Encryption protects data at rest by encrypting the entire Windows volume. If a device is lost or stolen, the data remains inaccessible without the recovery key.

#### BitLocker Management in Intune
Intune can silently enable and manage BitLocker on enrolled Windows 10/11 devices:

- **Silent enablement**: No user interaction required on modern hardware with TPM 2.0
- **Recovery key escrow**: Keys are automatically backed up to Azure AD
- **Encryption method**: AES-XTS 128-bit or 256-bit configurable
- **Startup authentication**: TPM only, TPM + PIN, TPM + startup key

#### Key BitLocker Settings
- Require BitLocker on OS drives
- Block write access to removable drives not protected by BitLocker
- Configure recovery options (recovery password, recovery key)
- Enforce encryption on fixed data drives
- Hide recovery options from end users to prevent key bypass
- Pre-provisioning: enable BitLocker before Windows setup completes (Autopilot scenarios)

#### BitLocker Encryption Methods

| Drive Type | Recommended Method | Notes |
|------------|--------------------|-------|
| OS Drive | AES-XTS 256-bit | Highest security; requires TPM 2.0 |
| Fixed Data Drive | AES-XTS 256-bit | Auto-unlock after OS drive is unlocked |
| Removable Drive | AES-CBC 128-bit | Wider compatibility for external use |

#### Viewing and Rotating Recovery Keys
Administrators and users can retrieve BitLocker recovery keys from:
- **Intune Admin Center**: Devices → Select device → Recovery keys
- **Azure AD Portal**: Devices → Select device → BitLocker keys
- **Self-service**: Users can access their own key via myaccount.microsoft.com

> 🔑 **Key Rotation**: Intune supports automatic BitLocker recovery key rotation after each use. Enable **Rotate BitLocker recovery passwords** in the BitLocker profile to ensure single-use recovery keys.

---

### 5. Windows Firewall Management

#### Firewall Profiles
Windows Firewall operates in three profiles, each configurable through Intune:
- **Domain** — Active on domain-joined network connections
- **Private** — Active on trusted private networks (home, small office)
- **Public** — Active on untrusted public networks (coffee shop, airport)

#### Firewall Policy Settings
- Enable/disable firewall per profile
- Block all incoming connections
- Allow/block specific applications
- Configure logging (dropped packets, successful connections)
- Stealth mode (hide device from network scans)

#### Custom Firewall Rules
Through Intune Endpoint Security > Firewall policies, you can define granular rules:
- Direction: Inbound or Outbound
- Protocol: TCP, UDP, ICMP
- Local/remote ports and addresses
- Application path (restrict rule to a specific .exe)
- Action: Allow or Block
- Profile scope: Domain, Private, Public

#### Firewall Monitoring and Logging
Firewall policy compliance can be tracked through:
- **Intune Monitor → Policy compliance**: Per-device firewall policy status
- **Windows Event Viewer**: Security log, filtered for firewall events
- **PowerShell verification**: `Get-NetFirewallProfile | Select Name, Enabled, LogFileName`
- **Defender for Endpoint**: Advanced hunting for network events blocked/allowed by the firewall

#### Common Firewall Scenarios in Enterprise

| Scenario | Configuration |
|----------|--------------|
| Block legacy SMBv1 | Inbound block, TCP port 445 + enable SMBv1 detection in ASR |
| Allow RDP only from jump server | Inbound allow, TCP 3389, remote address = jump server IP |
| Block outbound to known-bad IPs | Outbound block, specific remote address ranges |
| Allow application to reach internal API | Outbound allow, TCP port 8443, application path = app.exe |

---

### 6. Microsoft Defender for Endpoint Integration

#### What Is Microsoft Defender for Endpoint (MDE)?
MDE is an enterprise endpoint detection and response (EDR) platform that provides:
- Threat and vulnerability management
- Attack surface reduction
- Next-generation antivirus (Defender Antivirus)
- Endpoint detection and response
- Automated investigation and remediation
- Microsoft Secure Score for Devices

#### Connecting MDE to Intune
The integration is established through a **service-to-service connection** in the Intune admin center:
1. Navigate to Endpoint Security → Microsoft Defender for Endpoint
2. Enable the connector
3. Configure platform settings (Windows, Android, iOS, macOS)
4. Deploy the onboarding configuration profile to target devices

#### Benefits of Integration
- **Compliance signal**: MDE machine risk score feeds into Intune compliance policies
- **Security Tasks**: Vulnerability remediations surface in Intune
- **App protection**: Conditional Access can block access based on device threat level
- **Unified visibility**: Correlated alerts across identity, device, and workloads

#### MDE Machine Risk Score Levels

| Risk Score | Meaning | Recommended Compliance Action |
|------------|---------|-------------------------------|
| **None** | No threats detected | Allow access |
| **Low** | Potentially unwanted apps or low-severity vulnerabilities | Allow with monitoring |
| **Medium** | Active threats or high-severity vulnerabilities | Warn user; consider blocking sensitive apps |
| **High** | Active malware, ransomware indicators, or critical CVEs | Block access immediately |

#### Onboarding Methods
- **Intune MDM**: Recommended for cloud-managed devices; policy deploys the onboarding package
- **Group Policy**: For AD-joined devices not managed by Intune
- **Local script**: Manual onboarding for testing
- **Microsoft Endpoint Configuration Manager (MECM)**: Co-managed environments

#### MDE + Intune Integration Architecture
```
Device → [MDE Agent detects threat] → MDE Portal
                                           ↓
                              Risk Score published to Azure AD
                                           ↓
                         Intune Compliance Policy reads risk score
                                           ↓
                            Device marked Compliant / Non-compliant
                                           ↓
                           Conditional Access grants or blocks access
```

---

### 7. Security Baselines

#### What Are Security Baselines?
Security Baselines are pre-configured groups of Windows settings that represent the recommended security posture from Microsoft security teams. They implement settings from CIS Benchmarks, STIG, and Microsoft's own security guidance.

#### Available Baselines in Intune
- **Windows Security Baseline** — General hardening for Windows 10/11
- **Microsoft Defender for Endpoint Baseline** — Hardening settings specific to MDE
- **Microsoft Edge Security Baseline** — Browser hardening
- **Microsoft 365 Apps Security Baseline** — Office hardening
- **Windows 365 Cloud PC Security Baseline** — Cloud PC hardening

#### How Baselines Work
- Baselines are versioned; new versions are released as Windows updates ship
- You can compare baseline versions and update at your own pace
- Settings within a baseline can be overridden by more specific policies (profile wins on conflict)
- Baselines are assigned to user or device groups like any other policy
- You can **duplicate** a baseline profile to create a custom variant with specific overrides

#### Key Settings in the Windows Security Baseline (sample)

| Category | Setting | Baseline Default |
|----------|---------|-----------------|
| Accounts | Block Microsoft accounts | Enabled |
| Audit | Audit logon events | Success and Failure |
| Browser | SmartScreen | Enabled |
| Credential Guard | Virtualization Based Security | Enabled |
| Device Lock | Inactivity timeout | 15 minutes |
| Windows PowerShell | Script block logging | Enabled |
| Windows Defender | Cloud-delivered protection | Enabled |
| User Account Control | Admin approval mode | Enabled |

#### Upgrading Security Baseline Versions
When a new baseline version is released:
1. Navigate to **Endpoint Security → Security Baselines → [Baseline type]**
2. Select the profile and click **Change version**
3. Review what settings changed between versions using the **Compare** view
4. Update the profile to the new version
5. Monitor device compliance after update to catch any regressions

#### Security Baseline Reporting
- View per-setting compliance across all assigned devices
- Identify devices with conflicting settings (shows as **Conflict** or **Error** state)
- Track baseline version adoption over time
- Export reports for compliance audits

---

## Hands-On Labs

### Lab 6.1: Create Conditional Access Policy
**Objective**: Configure a Conditional Access policy that requires a compliant device for access to Microsoft 365 services

**Steps**:
1. Open the [Microsoft Entra admin center](https://entra.microsoft.com) and navigate to **Protection → Conditional Access → Policies**
2. Click **+ New policy** and name it `Require Compliant Device – M365`
3. Under **Users**, select **All users** (or a pilot group for testing)
4. Under **Target resources**, choose **Select apps** and add **Office 365** (or **Microsoft 365**)
5. Under **Conditions → Device platforms**, enable and select **Windows**, **iOS**, and **Android**
6. Under **Grant**, select **Grant access** and check **Require device to be marked as compliant**; click **Select**
7. Under **Session**, leave defaults (or add sign-in frequency if desired)
8. Set the policy to **Report-only** mode first, then click **Create**
9. After validating no legitimate users would be blocked, switch mode to **On**
10. Test by signing in from an enrolled compliant device and a personal non-enrolled device; confirm access is blocked on the latter

**Expected Outcome**: Only enrolled and compliant devices can access Microsoft 365 services; non-compliant devices receive an access-blocked page with a remediation link

---

### Lab 6.2: Create Compliance Policy
**Objective**: Create a Windows compliance policy that requires encryption, antivirus, and a minimum OS version

**Steps**:
1. In the [Intune admin center](https://intune.microsoft.com), navigate to **Devices → Compliance policies**
2. Click **+ Create policy**, select **Platform: Windows 10 and later**, then click **Create**
3. Name the policy `Windows 10 – Corporate Compliance`; click **Next**
4. Under **Device Health**, enable:
   - **Require BitLocker**: Yes
   - **Require Secure Boot to be enabled on the device**: Yes
   - **Require code integrity**: Yes
5. Under **Device Properties**, set:
   - **Minimum OS version**: `10.0.19041` (Windows 10 2004 or later)
6. Under **System Security**, configure:
   - **Require a password to unlock mobile devices**: Yes
   - **Required password type**: Alphanumeric
   - **Minimum password length**: 8
   - **Firewall**: Required
   - **Antivirus**: Required
   - **Antispyware**: Required
7. Under **Microsoft Defender for Endpoint**, set **Require the device to be at or under the machine risk score**: Medium
8. Click **Next** and configure **Actions for noncompliance**: Send email after 0 days; mark device non-compliant after 3 days
9. Assign to your test device group; click **Next → Create**
10. Force a device sync on a test device and review the compliance state under **Devices → Monitor → Device compliance**

**Expected Outcome**: Devices meeting all criteria show **Compliant**; devices missing settings show **Not compliant** with the specific failing settings listed

---

### Lab 6.3: Enable BitLocker on Windows Devices
**Objective**: Silently enable BitLocker on Windows devices with recovery keys escrowed to Azure AD

**Steps**:
1. In the Intune admin center, navigate to **Endpoint Security → Disk Encryption**
2. Click **+ Create Policy**, select **Platform: Windows 10 and later** and **Profile: BitLocker**; click **Create**
3. Name the policy `Windows – BitLocker Encryption`
4. Under **BitLocker – Base Settings**, configure:
   - **Enable full disk encryption for OS and fixed data drives**: Yes
   - **Require storage cards to be encrypted (mobile only)**: Yes (if applicable)
5. Under **BitLocker – OS Drive Settings**, configure:
   - **Additional authentication at startup**: Require
   - **Compatible TPM startup**: Allowed
   - **Compatible TPM startup PIN**: Blocked (for silent enablement)
   - **BitLocker recovery information saved to Azure AD**: Require
   - **Recovery information stored includes**: Recovery passwords and key packages
   - **Require device to back up recovery information to Azure AD**: Yes
   - **Enable BitLocker after recovery information is stored**: Yes
6. Under **BitLocker – Fixed Drive Settings**:
   - **Write access to fixed data-drive not protected by BitLocker**: Block
7. Under **BitLocker – Removable Drive Settings**:
   - **Write access to removable data-drive not protected by BitLocker**: Block
8. Assign to your Windows device test group; click **Next → Create**
9. On an enrolled Windows device, trigger a policy sync (**Settings → Accounts → Access work or school → Sync**)
10. After encryption completes, verify the recovery key in Intune under **Devices → Select device → Recovery keys**

**Expected Outcome**: BitLocker encrypts the OS drive silently; the recovery key is visible in Intune and Azure AD; write access to unencrypted removable drives is blocked

---

### Lab 6.4: Configure Firewall Rules
**Objective**: Deploy Windows Firewall settings and a custom inbound block rule via Intune

**Steps**:
1. In the Intune admin center, navigate to **Endpoint Security → Firewall**
2. Click **+ Create Policy**, select **Platform: Windows 10, Windows 11, and Windows Server** and **Profile: Windows Firewall**; click **Create**
3. Name the policy `Windows – Firewall Baseline`
4. Configure the **Domain**, **Private**, and **Public** network profiles:
   - **Enable firewall**: Yes (for all three profiles)
   - **Block inbound connections (public profile)**: Yes
   - **Stealth mode (public profile)**: Yes
   - **Log dropped packets**: Yes
   - **Log successful connections**: Yes
5. Click **Next → Create** to save the base firewall policy
6. Create a second policy: **Profile: Windows Firewall Rules**; name it `Windows – Block Telnet Inbound`
7. Under **Firewall Rules**, click **+ Add**:
   - **Name**: Block Telnet Inbound
   - **Direction**: Inbound
   - **Action**: Block
   - **Protocol**: TCP
   - **Local Ports**: 23
   - **Profile types**: Domain, Private, Public
   - **Enabled**: Yes
8. Assign both policies to your Windows test group; click **Next → Create**
9. On a test device, force a policy sync and run `Get-NetFirewallProfile | Select Name, Enabled` in PowerShell to verify profiles are enabled
10. Run `Get-NetFirewallRule -DisplayName "Block Telnet Inbound"` to confirm the custom rule was applied

**Expected Outcome**: Windows Firewall is enabled on all profiles; the custom block rule for TCP port 23 appears in the firewall rule list; public profile blocks unsolicited inbound connections

---

### Lab 6.5: Integrate Microsoft Defender for Endpoint
**Objective**: Connect Microsoft Defender for Endpoint to Intune and onboard a Windows device

**Steps**:
1. In the Intune admin center, navigate to **Endpoint Security → Microsoft Defender for Endpoint**
2. Verify the connection status shows **Available**; click **Open the Microsoft Defender for Endpoint console** and ensure your MDE tenant is active
3. Return to Intune; under **MDM Compliance Policy Settings**, enable:
   - **Connect Windows devices version 10.0.15063 and above to Microsoft Defender for Endpoint**: On
4. Click **Save**; wait for the connector status to show **Enabled**
5. Navigate to **Devices → Configuration profiles** and click **+ Create profile**
6. Select **Platform: Windows 10 and later** and **Profile type: Templates → Microsoft Defender for Endpoint (desktop devices running Windows 10 or later)**
7. Name it `Windows – MDE Onboarding`; click **Next**
8. Under **Microsoft Defender for Endpoint**, set **Sample sharing for all files**: Enable
9. Assign to your Windows test group; click **Next → Create**
10. On a test device, force a sync and confirm onboarding by opening **Windows Security → Device Security → Core isolation**; then verify the device appears in the **Microsoft 365 Defender portal** (security.microsoft.com) under **Assets → Devices**

**Expected Outcome**: The test device appears as **Onboarded** in Microsoft Defender for Endpoint; the device risk score feeds back into Intune compliance evaluation; alerts generated in MDE appear correlated with the Intune device record

---

### Lab 6.6: Apply Endpoint Security Baseline
**Objective**: Deploy the Windows Security Baseline to harden device configurations

**Steps**:
1. In the Intune admin center, navigate to **Endpoint Security → Security Baselines**
2. Select **Security Baseline for Windows 10 and later**
3. Click **+ Create profile** and name it `Windows – Security Baseline v24H2`
4. Review the default settings; note key hardened settings such as:
   - **Above Lock**: Disable Cortana above lock
   - **App Runtime**: Block Microsoft accounts for modern apps
   - **Credentials Delegation**: Remote host not allowing delegation of non-exportable credentials
   - **Windows Defender SmartScreen**: Enabled
   - **Windows Ink Workspace**: On, but disable desktop access
5. Expand **BitLocker** settings and verify OS drive encryption is required
6. Expand **Microsoft Edge** settings and review browser hardening defaults
7. Click **Next** and assign to your test Windows device group
8. Click **Create**
9. Force a sync on a test device; navigate to **Reports → Endpoint Security → Security Baselines** and select your baseline
10. Review the per-setting compliance report; identify any settings that conflict with existing profiles (shown as **Error** or **Conflict**)

**Expected Outcome**: The Security Baseline deploys to assigned devices; the per-setting report shows the majority of settings as **Succeeded**; any conflicts are surfaced for review and resolution

---

## Best Practices

✅ **Do's**:
- Start Conditional Access policies in **Report-only** mode before enforcing them
- Use pilot groups (a small set of test users) before rolling policies to all users
- Escrow BitLocker recovery keys to Azure AD for all Windows devices before requiring encryption
- Apply Security Baselines to all corporate Windows devices as a hardening foundation
- Use the **Machine Risk Score** from MDE in compliance policies to block compromised devices
- Set compliance grace periods (e.g., 3 days) to give users time to remediate before losing access
- Review Security Tasks from MDE regularly and close them through Intune
- Version-pin Security Baselines and plan upgrades as part of your patch cycle
- Enable logging on Windows Firewall for all profiles in corporate environments
- Test firewall rule changes in Report-only/Audit mode before blocking traffic

❌ **Don'ts**:
- Do not enable Conditional Access in **On** mode without first validating in **Report-only** mode
- Do not require BitLocker without ensuring recovery keys are escrowed — you risk data loss
- Do not assign Security Baselines to personal (BYOD) devices without user consent
- Do not set compliance grace period to 0 days without thorough communication to users
- Do not create overlapping Conditional Access policies with conflicting grant controls
- Do not ignore MDE Security Tasks — unresolved vulnerabilities increase risk exposure
- Do not mix Endpoint Security firewall policies with legacy GPO firewall settings on the same device
- Do not rely solely on compliance policies for security — pair with Conditional Access to enforce access controls
- Do not skip testing Defender for Endpoint onboarding on a single device before broad deployment
- Do not block all inbound traffic on Domain firewall profiles — this may break legitimate enterprise services

---

## Common Issues and Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| **Device shows "Not compliant" despite meeting all requirements** | Device has not checked in since policy was applied | Force device sync: **Settings → Accounts → Access work or school → Sync**, or use Intune remote sync action |
| **BitLocker fails to enable silently** | TPM is not present, not enabled in BIOS, or firmware is outdated | Verify TPM status with `tpm.msc`; enable TPM in BIOS; update device firmware |
| **BitLocker recovery key not appearing in Intune** | Key escrow failed due to a network issue or Azure AD connectivity problem | Re-run `manage-bde -protectors -adbackup C: -id {KEY-ID}` on the device; check Azure AD connectivity |
| **Conditional Access blocks legitimate users** | User's device is enrolled but compliance evaluation has not completed yet | Wait for the compliance check cycle (~15 min); force a device sync; review the compliance state in Intune |
| **Security Baseline shows "Conflict" for some settings** | Another profile or GPO is also managing the same setting | Identify the conflicting policy using the per-setting report; remove the duplicate setting from one policy |
| **MDE connector shows "Unavailable"** | MDE tenant is not licensed or the service connection has not been established | Verify MDE licensing (requires Microsoft Defender for Endpoint Plan 1 or 2); re-establish the service connection in Endpoint Security settings |
| **Firewall rule deployed but not visible on device** | Policy has not synced or there is a WMI/CSP error | Check Intune Management Extension logs (`%ProgramData%\Microsoft\IntuneManagementExtension\Logs`); re-sync device |
| **Users locked out after Conditional Access policy enforcement** | Break-glass / emergency access accounts not excluded from policy | Always exclude break-glass accounts from all Conditional Access policies; add them to an exclusion group |
| **Compliance policy not evaluating MDE risk score** | MDE connector not enabled or device not yet onboarded to MDE | Enable the MDE connector in Endpoint Security; verify device onboarding status in MDE portal |
| **Security Baseline settings revert after restart** | Conflicting GPO is overwriting Intune-applied settings | Remove or disable conflicting GPOs; use Intune as the authoritative policy source for cloud-managed devices |

---

## Assessment Questions

1. **What is the relationship between Intune compliance policies and Azure AD Conditional Access?**

   <details>
   <summary>Answer</summary>
   Intune compliance policies evaluate whether a device meets defined security requirements and publish a **Compliant** or **Non-compliant** state to Azure AD. Conditional Access policies read this compliance state as a signal and use it as a condition to grant or block access to cloud resources. Neither system enforces access in isolation — they work together as part of a Zero Trust architecture.
   </details>

2. **What happens if BitLocker is required in a compliance policy but the recovery key has not been escrowed to Azure AD?**

   <details>
   <summary>Answer</summary>
   If the device does not have a recovery key backed up to Azure AD, the compliance policy can still mark the device as non-compliant for the BitLocker setting. To prevent data loss risk, the BitLocker Endpoint Security profile should be configured with **"Require device to back up recovery information to Azure AD before enabling BitLocker"** set to **Yes**, ensuring the key is escrowed before encryption is enforced.
   </details>

3. **Why should Conditional Access policies always be tested in Report-only mode first?**

   <details>
   <summary>Answer</summary>
   Report-only mode allows administrators to see what the policy *would* do (which users and sign-ins would be affected) without actually blocking or granting access. This prevents accidental lockouts, especially for administrators and service accounts. After reviewing the sign-in logs in Report-only mode and confirming no legitimate access would be broken, the policy can be safely switched to **On**.
   </details>

4. **What is a Security Baseline in Intune and how does it differ from a Device Configuration profile?**

   <details>
   <summary>Answer</summary>
   A Security Baseline is a pre-built collection of Microsoft-recommended security settings for a specific product (e.g., Windows, Edge, MDE). It is versioned and maintained by Microsoft security teams. A Device Configuration profile is a custom policy where an administrator manually selects individual settings. Security Baselines provide a faster path to a hardened baseline configuration without requiring deep knowledge of every individual setting, while Device Configuration profiles offer more granular control for custom requirements.
   </details>

5. **A user reports they cannot access SharePoint from their personal phone. After investigation, you find the phone is not enrolled in Intune. What is the most likely cause and what are two possible resolutions?**

   <details>
   <summary>Answer</summary>
   **Cause**: A Conditional Access policy requires a compliant (Intune-enrolled) device for access to Microsoft 365 services, and the personal phone is not enrolled, so it is not compliant.
   
   **Resolution Option 1**: Enroll the personal device in Intune (BYOD enrollment) and ensure it meets all compliance policy requirements. Once compliant, access will be granted.
   
   **Resolution Option 2**: If BYOD enrollment is not appropriate, modify the Conditional Access policy to allow access from personal devices when an **App Protection Policy (MAM without enrollment)** is applied (e.g., using the Outlook mobile app with an Intune MAM policy). This allows access without requiring full device enrollment.
   </details>

---

## Key Resources

- [Conditional Access in Azure AD Documentation](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Intune Device Compliance Policies](https://docs.microsoft.com/mem/intune/protect/device-compliance-get-started)
- [Manage BitLocker Policy with Intune](https://docs.microsoft.com/mem/intune/protect/encrypt-devices)
- [Endpoint Security Firewall Policies](https://docs.microsoft.com/mem/intune/protect/endpoint-security-firewall-policy)
- [Microsoft Defender for Endpoint Integration with Intune](https://docs.microsoft.com/mem/intune/protect/advanced-threat-protection)
- [Security Baselines in Intune](https://docs.microsoft.com/mem/intune/protect/security-baselines)
- [Microsoft Secure Score for Devices](https://docs.microsoft.com/microsoft-365/security/defender/microsoft-secure-score-devices)
- [Zero Trust with Microsoft Intune](https://docs.microsoft.com/mem/intune/fundamentals/zero-trust-with-microsoft-intune)

---

## Next Steps

After completing this module:
1. **Review** your organization's current security posture against the Security Baseline settings covered in Lab 6.6
2. **Plan** a phased Conditional Access rollout — start with Report-only, then enforce for pilot groups, then all users
3. **Inventory** Windows devices to ensure TPM 2.0 is present and enabled before deploying BitLocker policies
4. **Validate** that Microsoft Defender for Endpoint is licensed and the Intune connector is active before deploying compliance policies that reference machine risk scores
5. **Proceed** to Module-07-Remote-Help-Support to learn how to support end users remotely using Intune Remote Help and troubleshooting tools

---

**Module Status**: Ready for Training
**Last Updated**: February 2026
**Duration**: 5-7 hours (including labs)


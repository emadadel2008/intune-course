# Lab Guide: Contoso Ltd Enterprise Intune Deployment

**Module 11 Capstone — Detailed Step-by-Step Walkthrough**

---

## Overview

This lab guide walks you through every step required to migrate **Contoso Ltd's 500-device estate** to Microsoft Intune. Work through the phases in order; each phase builds on the previous one.

**Lab environment requirements:**
- Microsoft 365 E3 or E5 trial tenant (or dev tenant from M365 Developer Program)
- At least one physical or virtual Windows 11 device (or Windows 11 VM with TPM 2.0 emulated)
- Optional: physical iOS device and Android device for enrollment labs
- Global Administrator or Intune Service Administrator role in the tenant
- PowerShell 7.x with Microsoft Graph SDK installed

**Estimated total time:** 5–8 hours across all phases  
**Difficulty:** Advanced

---

## Phase 1: Tenant and Infrastructure Setup

**Goal:** Configure the Microsoft 365 tenant with all prerequisites Intune needs before any device is enrolled.

**Estimated time:** 60–90 minutes

---

### Step 1.1 – Set the MDM Authority

1. Sign in to the **Microsoft Intune admin center** at `https://intune.microsoft.com` with your Global Administrator account.
2. Navigate to **Tenant administration > Tenant status**.
3. Confirm the **MDM authority** reads **Microsoft Intune**. If it still reads *Configuration Manager*, do the following:
   - Go to **Tenant administration > Cloud attach** and switch the authority to Intune-only for this lab.
4. Record the **Tenant name** and **Tenant ID** — you will need these in later steps.

**Verification:** Tenant status page shows *MDM authority: Microsoft Intune* with a green indicator.

---

### Step 1.2 – Assign Licenses

1. In the **Microsoft 365 admin center** (`admin.microsoft.com`), go to **Billing > Licenses**.
2. Select your **Microsoft 365 E3** or **EMS E3** license.
3. Assign licenses to the following test accounts (create them if they don't exist):
   - `admin-contoso@<yourtenant>.onmicrosoft.com` (Global Admin)
   - `user-chicago@<yourtenant>.onmicrosoft.com` (Standard user, Chicago)
   - `user-london@<yourtenant>.onmicrosoft.com` (Standard user, London)
   - `user-newyork@<yourtenant>.onmicrosoft.com` (Standard user, New York)
4. Ensure each account has **Intune** and **Entra ID P2** components enabled in the license assignment.

**Verification:** In Intune admin center > Users > All users, all four accounts appear with a license indicator.

---

### Step 1.3 – Create Entra ID Groups

Create the following security groups (all as **assigned**, not dynamic, for simplicity in this lab):

| Group Name | Members | Purpose |
|---|---|---|
| `Contoso-Windows-Devices` | (device objects added during enrollment) | Windows policy targeting |
| `Contoso-iOS-Devices` | (device objects added during enrollment) | iOS policy targeting |
| `Contoso-Android-Devices` | (device objects added during enrollment) | Android policy targeting |
| `Contoso-All-Users` | user-chicago, user-london, user-newyork | App and user policy targeting |
| `Contoso-Chicago-Users` | user-chicago | Location-specific policies |
| `Contoso-London-Users` | user-london | GDPR-specific policies |
| `Contoso-Pilot-Devices` | (add first test device after enrollment) | Pilot ring for staged rollout |

**Steps:**
1. In **Entra ID admin center** (`entra.microsoft.com`), go to **Groups > All groups > New group**.
2. Set Group type: **Security**, Name: (as above), Membership type: **Assigned**.
3. Repeat for all seven groups.

**Verification:** All seven groups appear in **Entra ID > Groups > All groups**.

---

### Step 1.4 – Configure Apple APNs Certificate

1. In Intune admin center, go to **Tenant administration > Connectors and tokens > Apple MDM Push certificate**.
2. Click **Configure** and follow the wizard:
   a. Download the **Certificate Signing Request (CSR)** file.
   b. Go to `https://identity.apple.com` and sign in with your **Apple ID** (use a corporate Apple ID, not personal).
   c. Upload the CSR and download the resulting `.pem` certificate.
   d. Return to Intune and upload the `.pem` file.
3. Note the **expiration date** — this certificate must be renewed annually with the same Apple ID.

**Verification:** APNs certificate status shows **Active** with an expiration date approximately one year from today.

---

### Step 1.5 – Configure Android Enterprise

1. In Intune admin center, go to **Tenant administration > Connectors and tokens > Managed Google Play**.
2. Click **Connect** and sign in with a **Google account** dedicated to Contoso (not a personal Gmail).
3. Accept the terms and complete the Managed Google Play binding.
4. Return to **Enrollment > Android enrollment** and confirm that **Corporate-owned fully managed** and **Work profile** options are available.

**Verification:** Managed Google Play status shows **Connected** with the bound Google account email displayed.

---

### Step 1.6 – Configure Windows Autopilot Prerequisites

1. In Intune admin center, navigate to **Enrollment > Windows enrollment > Windows Autopilot Deployment Service**.
2. Confirm **Autopilot** is listed as an available connector.
3. Create an **Autopilot deployment profile:**
   a. Go to **Enrollment > Windows enrollment > Deployment profiles > Create profile > Windows PC**.
   b. Name: `Contoso-Autopilot-UserDriven`
   c. Deployment mode: **User-driven**
   d. Join type: **Azure AD joined**
   e. OOBE settings: Hide EULA, hide privacy settings, hide change account options, allow pre-provisioning: **Yes**
   f. Assign to group: `Contoso-Pilot-Devices`
4. Create an **Enrollment Status Page (ESP) profile:**
   a. Go to **Enrollment > Windows enrollment > Enrollment Status Page > Create**.
   b. Name: `Contoso-ESP`
   c. Show app and profile configuration progress: **Yes**
   d. Block device use until all apps and profiles are installed: **Yes**
   e. Time limit (minutes): **90**
   f. Assign to: **All devices** (adjust to pilot group in production).

**Verification:** Deployment profile and ESP profile both show in their respective lists with assignment status *Assigned*.

---

### Phase 1 Verification Checklist

- [ ] MDM authority is set to Microsoft Intune
- [ ] Four test user accounts created and licensed
- [ ] Seven Entra ID groups created
- [ ] Apple APNs certificate active and not expiring within 60 days
- [ ] Managed Google Play bound to tenant
- [ ] Autopilot deployment profile created and assigned
- [ ] Enrollment Status Page profile created and assigned

---

## Phase 2: Device Enrollment

**Goal:** Enroll test devices representing Windows, iOS, and Android platforms using the enrollment methods Contoso will use in production.

**Estimated time:** 60–90 minutes

---

### Step 2.1 – Upload Windows Device Hardware Hash (Autopilot)

**Option A – Physical device or VM with exported hash:**

1. On the Windows device to be enrolled (must be a fresh/reset device at OOBE), press **Shift+F10** to open a command prompt.
2. Run the following PowerShell command to capture and upload the hardware hash:
   ```powershell
   Install-Script -Name Get-WindowsAutoPilotInfo -Force
   Get-WindowsAutoPilotInfo -Online
   ```
3. When prompted, sign in with your Intune administrator credentials.
4. The script uploads the hardware hash directly to the Autopilot service.

**Option B – Import a CSV manually:**

1. On any device (not the target device), run:
   ```powershell
   Install-Script -Name Get-WindowsAutoPilotInfo -Force
   Get-WindowsAutoPilotInfo -OutputFile C:\autopilot-hash.csv
   ```
2. Copy `autopilot-hash.csv` to a machine where you can access the Intune portal.
3. In Intune admin center: **Enrollment > Windows enrollment > Devices > Import**.
4. Upload the CSV file.

5. Wait 5–10 minutes for the device to appear in **Enrollment > Windows enrollment > Devices** with the assigned group tag.
6. Add the device object to the `Contoso-Pilot-Devices` group so the deployment profile applies.

**Verification:** Device appears in **Enrollment > Windows enrollment > Devices** with Profile status *Assigned*.

---

### Step 2.2 – Complete Autopilot OOBE

1. Reset the Windows device (or power on a freshly reimaged VM) to begin OOBE.
2. Connect to Wi-Fi or Ethernet with internet access.
3. Select your region and keyboard layout.
4. The Autopilot flow intercepts the next screen — you should see the **Contoso Ltd sign-in page** (customized via company branding if configured).
5. Sign in with `user-chicago@<yourtenant>.onmicrosoft.com` and the test password.
6. If MFA is configured, complete the MFA challenge.
7. The **Enrollment Status Page** appears, showing progress for device setup and account setup phases.
8. Wait for all items to complete (this may take 10–30 minutes depending on app assignments).
9. Desktop appears — the device is now enrolled.

**Verification:** In Intune admin center > Devices > All devices, the Windows device appears with:
- Management type: **MDM**
- Ownership: **Corporate**
- Compliance: **Compliant** (or Pending evaluation)

---

### Step 2.3 – Enroll an iOS Device via ADE

> **Note:** This step requires access to Apple Business Manager (ABM) and a supervised iOS device. If you don't have ABM access, use the manual profile method below as an alternative.

**ABM Method:**
1. In Apple Business Manager (`business.apple.com`), go to **Devices** and confirm the iOS device appears (it must have been purchased from Apple or an authorized reseller linked to ABM).
2. In ABM: **Settings > MDM servers** — ensure your Intune MDM server is listed and the device is assigned to it.
3. In Intune admin center: **Enrollment > Apple enrollment > Enrollment program tokens > [Your token] > Profiles > Create profile**.
   - Name: `Contoso-ADE-iOS`
   - User affinity: **Enroll with user affinity**
   - Supervised: **Yes**
   - Locked enrollment: **Yes**
4. Assign the profile to the device in the ABM-linked device list.
5. Factory reset the iOS device; during setup the management profile installs automatically.

**Manual Profile Method (lab alternative):**
1. In Intune admin center: **Devices > iOS/iPadOS > iOS enrollment > Apple Configurator > Profiles > Create**.
2. Download the resulting enrollment profile `.mobileconfig` file.
3. Email the file to `user-chicago` or host it on a web server the device can reach.
4. On the iOS device, open the profile from Mail or Safari and install it via **Settings > General > VPN & Device Management**.

**Verification:** Device appears in **Devices > All devices** with OS type *iOS/iPadOS* and management type *MDM*.

---

### Step 2.4 – Enroll an Android Device (Work Profile)

1. On the Android device, go to **Settings > Accounts > Add account** (the exact path varies by device manufacturer).
2. Alternatively, from the Intune Company Portal app:
   a. Install **Intune Company Portal** from Google Play.
   b. Open the app and sign in with `user-newyork@<yourtenant>.onmicrosoft.com`.
   c. Follow the prompts to set up a **Work Profile**.
3. The work profile creates a separate, managed profile on the device. Corporate apps install in the work profile; personal apps remain in the personal profile.
4. Accept the management policy and complete the enrollment wizard.

**Verification:** Device appears in **Devices > All devices** with OS type *Android* and enrollment type *Android Enterprise – Work Profile*.

---

### Step 2.5 – Configure Enrollment Restrictions

1. In Intune admin center: **Enrollment > Enrollment restrictions**.
2. Modify the **Default** device type restriction:
   - Block **personally owned** Windows devices (Contoso only allows corporate Windows enrollment).
   - Allow personally owned iOS and Android (BYOD allowed for mobile).
   - Minimum OS: iOS 16.0, Android 11.
3. Set device limit restriction to **5** devices per user.

**Verification:** Attempting to enroll a 6th device with a test user results in an enrollment failure with a "Device limit reached" error.

---

### Phase 2 Verification Checklist

- [ ] Windows device enrolled via Autopilot; appears in Devices > All devices
- [ ] iOS device enrolled; supervised flag set to Yes (if using ADE)
- [ ] Android device enrolled with work profile
- [ ] All three devices show in their respective Entra ID groups (if using dynamic groups)
- [ ] Enrollment restrictions configured with minimum OS versions
- [ ] Device limit restriction set to 5

---

## Phase 3: Application Deployment

**Goal:** Build out the Contoso app portfolio and deliver apps to devices.

**Estimated time:** 60–90 minutes

---

### Step 3.1 – Package and Deploy a Win32 Application

**Application:** Deploy **7-Zip** as a representative Win32 app.

1. Download 7-Zip `.exe` installer from `7-zip.org`.
2. Download and install the **Microsoft Win32 Content Prep Tool** (`IntuneWinAppUtil.exe`) from GitHub:
   `https://github.com/microsoft/Microsoft-Win32-Content-Prep-Tool`
3. Create a staging folder: `C:\IntuneApps\7zip\`
4. Copy the 7-Zip installer to the staging folder.
5. Open an elevated PowerShell prompt and run:
   ```powershell
   .\IntuneWinAppUtil.exe -c "C:\IntuneApps\7zip" -s "7z2301-x64.exe" -o "C:\IntuneApps\Output"
   ```
6. This produces `7z2301-x64.intunewin` in the output folder.
7. In Intune admin center: **Apps > All apps > Add > App type: Windows app (Win32)**.
8. Upload the `.intunewin` file.
9. Configure:
   - **Name:** 7-Zip 23.01
   - **Install command:** `7z2301-x64.exe /S`
   - **Uninstall command:** `"C:\Program Files\7-Zip\Uninstall.exe" /S`
   - **Install behavior:** System
   - **Device restart behavior:** No specific action
10. Detection rule: **File** — Path: `C:\Program Files\7-Zip`, File: `7z.exe`, Detection method: File or folder exists.
11. Assign as **Required** to `Contoso-Windows-Devices`.

**Verification:** After device sync, 7-Zip appears in **Apps > Monitor > App install status** with *Installed* state for the test device.

---

### Step 3.2 – Deploy a Microsoft 365 Apps Suite

1. In Intune admin center: **Apps > All apps > Add > Microsoft 365 Apps > Windows 10 and later**.
2. Configure the suite:
   - Apps to include: Word, Excel, PowerPoint, Outlook, Teams, OneDrive
   - Update channel: **Current Channel**
   - Remove other versions: **Yes**
   - Architecture: **64-bit**
3. Assign as **Required** to `Contoso-All-Users`.

**Verification:** Microsoft 365 Apps appear on the enrolled Windows device after policy sync (allow 30–60 minutes for download and installation).

---

### Step 3.3 – Add a Web App (Shortcut)

1. In Intune admin center: **Apps > All apps > Add > Other > Web link**.
2. Configure:
   - **Name:** Contoso Intranet
   - **URL:** `https://contoso.sharepoint.com`
   - **Icon:** Upload a 512×512 PNG (optional)
3. Assign as **Available** to `Contoso-All-Users`.

**Verification:** Web app appears in the **Company Portal** app on enrolled devices.

---

### Step 3.4 – Configure an App Protection Policy (MAM) for iOS

1. In Intune admin center: **Apps > App protection policies > Create policy > iOS/iPadOS**.
2. Configure:
   - **Name:** Contoso-MAM-iOS
   - **Target apps:** Microsoft Outlook, Microsoft Teams, Microsoft OneDrive
3. Data protection settings:
   - Backup org data to iTunes: **Block**
   - Send org data to other apps: **Policy managed apps**
   - Receive data from other apps: **Policy managed apps**
   - Restrict cut, copy, paste: **Policy managed apps with paste in**
   - Screen capture and Google Assistant: **Block**
4. Access requirements:
   - PIN for access: **Require**
   - PIN type: **Numeric**
   - Number of values: **6**
   - Biometrics instead of PIN: **Allow**
5. Conditional launch:
   - Offline grace period: **720 minutes** (block access)
   - Jailbroken/rooted devices: **Block access**
6. Assign to `Contoso-All-Users`.

**Verification:** On the iOS device, open Outlook (signed in with the Contoso account). Attempt to paste Contoso email content into the personal Notes app — it should be blocked. A PIN prompt should appear when reopening the app after a period of inactivity.

---

### Step 3.5 – Managed Google Play App (Android)

1. In Intune admin center: **Apps > All apps > Add > Managed Google Play app**.
2. Search for **Microsoft Outlook** in the Managed Google Play store.
3. Click **Select** and then **Sync**.
4. After syncing, the app appears in All apps; assign it as **Required** to `Contoso-Android-Devices`.

**Verification:** Outlook installs automatically in the work profile on the enrolled Android device.

---

### Phase 3 Verification Checklist

- [ ] 7-Zip deployed as Required Win32 app; shows Installed on Windows test device
- [ ] Microsoft 365 Apps suite deployed; apps appear on Windows device
- [ ] Contoso Intranet web app visible in Company Portal
- [ ] iOS MAM policy active; copy-paste to unmanaged apps blocked
- [ ] Managed Google Play app deployed to Android work profile

---

## Phase 4: Security & Compliance Configuration

**Goal:** Enforce security baselines, compliance policies, and conditional access to ensure only healthy, managed devices access corporate resources.

**Estimated time:** 60–90 minutes

---

### Step 4.1 – Create a Windows Compliance Policy

1. In Intune admin center: **Devices > Compliance policies > Create policy > Windows 10 and later**.
2. Name: `Contoso-Windows-Compliance`
3. Configure the following settings:

**Device Health:**
- Require BitLocker: **Require**
- Require Secure Boot: **Require**
- Require code integrity: **Require**

**Device Properties:**
- Minimum OS version: `10.0.19045` (Windows 10 22H2) — adjust to `10.0.22621` for Windows 11 22H2 if required
- Maximum OS version: leave blank

**System Security:**
- Require password: **Require**
- Minimum password length: **8**
- Password type: **Alphanumeric**
- Password expiration: **90** days
- Firewall: **Require**
- Antivirus: **Require**
- Antispyware: **Require**
- Microsoft Defender Antimalware: **Require**
- Microsoft Defender Antimalware minimum version: `4.18` (or current)
- Real-time protection: **Require**

4. **Actions for noncompliance:**
   - 0 days: Mark device non-compliant
   - 3 days: Send email to end user
   - 7 days: Remotely lock device

5. Assign to `Contoso-Windows-Devices`.

**Verification:** In **Devices > Monitor > Device compliance**, the test Windows device should show *Compliant* (may take 10–15 minutes after first sync).

---

### Step 4.2 – Create an iOS Compliance Policy

1. **Devices > Compliance policies > Create policy > iOS/iPadOS**.
2. Name: `Contoso-iOS-Compliance`
3. Settings:
   - Minimum OS version: `16.0`
   - Jailbroken devices: **Block**
   - Require password: **Require**
   - Minimum password length: **6**
   - Data storage encryption: **Require**
4. Actions for noncompliance: same 3-day email, 7-day lock as Windows policy.
5. Assign to `Contoso-iOS-Devices`.

---

### Step 4.3 – Apply a Windows Security Baseline

1. In Intune admin center: **Endpoint security > Security baselines > Windows security baseline**.
2. Click **Create profile**.
   - Name: `Contoso-Windows-SecurityBaseline`
   - Accept Microsoft's recommended settings (review and note any that conflict with Contoso's operational requirements).
   - Common review items: BitLocker settings, Windows Defender credential guard, UAC behavior.
3. Assign to `Contoso-Windows-Devices`.

> **Important:** Security baselines can conflict with other configuration profiles. Review the **Assignment conflict** report at **Devices > Monitor > Assignment failures** after applying.

**Verification:** Profile shows in **Endpoint security > Security baselines** with assignment status *Assigned to X devices*.

---

### Step 4.4 – Configure Endpoint Security – Antivirus Policy

1. **Endpoint security > Antivirus > Create policy > Windows 10 and later > Microsoft Defender Antivirus**.
2. Name: `Contoso-Defender-AV`
3. Settings:
   - Cloud-delivered protection: **Enabled**
   - Cloud-delivered protection level: **High**
   - Automatic sample submission: **Send safe samples automatically**
   - Real-time protection: **Monitor all files**
   - Behavior monitoring: **Enabled**
   - Scan all downloaded files: **Enabled**
   - Scheduled scan type: **Quick scan**
   - Day of week to run scan: **Saturday**
   - Time of day to run scan: **02:00**
4. Assign to `Contoso-Windows-Devices`.

---

### Step 4.5 – Configure Conditional Access

1. In **Entra ID admin center > Security > Conditional Access > New policy**.
2. **Policy 1: Require compliant device for cloud apps**
   - Name: `CA-Contoso-RequireCompliantDevice`
   - Users: **Include — Contoso-All-Users**; **Exclude — admin-contoso** (exclude the break-glass account)
   - Cloud apps: **Office 365** (includes Exchange, SharePoint, Teams)
   - Conditions: Device platforms — **Include: Windows, iOS, Android**
   - Grant: **Grant access — Require device to be marked as compliant**
   - Session: leave default
   - Enable policy: **Report-only** first, then **On** after validating with the What If tool.

3. **Policy 2: Block legacy authentication**
   - Name: `CA-Contoso-BlockLegacyAuth`
   - Users: **All users**; Exclude admin account
   - Cloud apps: **All cloud apps**
   - Conditions: Client apps — **Exchange ActiveSync clients**, **Other clients**
   - Grant: **Block access**
   - Enable policy: **On**

**Verification (Report-only mode):**
1. Sign in with `user-chicago` credentials from a managed compliant device.
2. In **Entra ID > Monitoring > Sign-in logs**, find the sign-in event.
3. Expand the **Conditional Access** tab; the policy should show *Report-only: Success* for the compliant device sign-in.
4. Once validated, switch the first policy from Report-only to **On**.

---

### Step 4.6 – Configure a VPN Profile (Windows)

1. **Devices > Configuration profiles > Create profile > Windows 10 and later > VPN**.
2. Name: `Contoso-VPN-Profile`
3. Connection type: **Automatic** (or the VPN vendor your lab supports — IKEv2 for native Windows VPN)
4. Server address: enter your lab VPN gateway address (or a placeholder for the exercise)
5. Always-on VPN: **Enable**
6. Authentication method: **Certificates**
7. Assign to `Contoso-Windows-Devices`.

> For a lab without a real VPN gateway, this profile will deploy but fail to connect. The purpose of this step is to practice profile deployment, not actual VPN connectivity.

---

### Step 4.7 – Deploy a SCEP Certificate Profile (Simulated)

1. **Devices > Configuration profiles > Create profile > Windows 10 and later > SCEP certificate**.
2. Name: `Contoso-Device-Certificate`
3. Certificate type: **Device**
4. Subject name format: `CN={{DeviceId}}`
5. Subject alternative name: `DNS = {{DeviceId}}.contoso.com`
6. Certificate validity period: **1 year**
7. Key size: **2048**
8. Hash algorithm: **SHA-2**
9. Extended key usage: **Client Authentication**
10. Renewal threshold: **20%**
11. SCEP server URL: enter your NDES server URL (or a placeholder)
12. Assign to `Contoso-Windows-Devices`.

> In a production environment this requires NDES and the Intune Certificate Connector to be deployed on-premises. In this lab, the profile will show *Pending* without a functional NDES endpoint — that is expected.

---

### Phase 4 Verification Checklist

- [ ] Windows compliance policy deployed; test device shows Compliant
- [ ] iOS compliance policy deployed and assigned
- [ ] Windows security baseline applied; no unresolved conflicts
- [ ] Defender AV policy deployed
- [ ] Conditional access policy CA-Contoso-RequireCompliantDevice in Report-only mode (validate), then On
- [ ] Block legacy auth CA policy enabled
- [ ] VPN profile deployed to Windows devices
- [ ] SCEP certificate profile deployed (Pending is acceptable without NDES)

---

## Phase 5: Automation and Reporting

**Goal:** Automate common administrative tasks and build the compliance reporting dashboard Contoso's security team needs.

**Estimated time:** 60–90 minutes

---

### Step 5.1 – Install Microsoft Graph PowerShell SDK

On your admin workstation, open **PowerShell 7** as administrator and run:

```powershell
Install-Module Microsoft.Graph -Scope CurrentUser -Force
Install-Module Microsoft.Graph.Beta -Scope CurrentUser -Force
```

Verify installation:
```powershell
Get-Module Microsoft.Graph -ListAvailable | Select-Object Name, Version
```

---

### Step 5.2 – Authenticate to Graph

```powershell
Connect-MgGraph -Scopes `
    "DeviceManagementManagedDevices.Read.All",`
    "DeviceManagementConfiguration.Read.All",`
    "DeviceManagementApps.Read.All",`
    "Directory.Read.All"
```

A browser window opens for interactive authentication. Sign in with your Intune admin account and grant the requested permissions.

Verify the connection:
```powershell
Get-MgContext
```

Expected output includes your signed-in account and the granted scopes.

---

### Step 5.3 – Script: List All Non-Compliant Devices

Create a file `Get-NonCompliantDevices.ps1` with the following content:

```powershell
<#
.SYNOPSIS
    Retrieves all non-compliant Intune-managed devices and exports to CSV.
.DESCRIPTION
    Queries Microsoft Graph for managed devices where complianceState is nonCompliant
    and exports the results to a timestamped CSV file.
#>

param(
    [string]$OutputPath = ".\NonCompliantDevices_$(Get-Date -Format 'yyyyMMdd_HHmmss').csv"
)

Connect-MgGraph -Scopes "DeviceManagementManagedDevices.Read.All" -NoWelcome

$devices = Get-MgDeviceManagementManagedDevice -Filter "complianceState eq 'nonCompliant'" -All

$report = $devices | Select-Object `
    @{N='DeviceName';    E={$_.DeviceName}},
    @{N='UserPrincipalName'; E={$_.UserPrincipalName}},
    @{N='OS';            E={$_.OperatingSystem}},
    @{N='OSVersion';     E={$_.OsVersion}},
    @{N='ComplianceState';E={$_.ComplianceState}},
    @{N='LastSync';      E={$_.LastSyncDateTime}},
    @{N='EnrollmentDate';E={$_.EnrolledDateTime}},
    @{N='Ownership';     E={$_.ManagedDeviceOwnerType}}

$report | Export-Csv -Path $OutputPath -NoTypeInformation

Write-Host "Exported $($report.Count) non-compliant devices to: $OutputPath" -ForegroundColor Yellow
```

Run the script:
```powershell
.\Get-NonCompliantDevices.ps1
```

**Verification:** CSV file created in the current directory; open it to confirm device data is populated correctly.

---

### Step 5.4 – Script: Bulk Sync All Devices

```powershell
<#
.SYNOPSIS
    Sends a sync request to all Intune-managed devices.
.NOTES
    Use during maintenance windows to force policy refresh across the fleet.
#>

Connect-MgGraph -Scopes "DeviceManagementManagedDevices.ReadWrite.All" -NoWelcome

$devices = Get-MgDeviceManagementManagedDevice -All

$synced = 0
foreach ($device in $devices) {
    try {
        Invoke-MgSyncDeviceManagementManagedDevice -ManagedDeviceId $device.Id
        Write-Host "  Synced: $($device.DeviceName)" -ForegroundColor Green
        $synced++
    }
    catch {
        Write-Warning "  Failed to sync $($device.DeviceName): $_"
    }
}

Write-Host "`nSync requests sent to $synced / $($devices.Count) devices." -ForegroundColor Cyan
```

**Note:** Sync requests are queued; actual device response depends on the device being online and the push notification channel (WNS for Windows, APNs for iOS).

---

### Step 5.5 – Script: Get App Installation Status Summary

```powershell
<#
.SYNOPSIS
    Reports installation status for all apps across all devices.
#>

Connect-MgGraph -Scopes "DeviceManagementApps.Read.All" -NoWelcome

$apps = Get-MgDeviceManagementMobileApp -All | Where-Object { $_.AdditionalProperties.'@odata.type' -notlike '*webApp*' }

Write-Host "`n=== App Installation Summary ===" -ForegroundColor Cyan
Write-Host ("{0,-45} {1,-12}" -f "App Name", "Display Name")
Write-Host ("-" * 60)

foreach ($app in $apps | Select-Object -First 20) {
    $status = Get-MgDeviceManagementMobileAppInstallSummary -MobileAppId $app.Id
    Write-Host ("{0,-45} {1} installed / {2} failed" -f `
        $app.DisplayName,
        $status.InstalledDeviceCount,
        $status.FailedDeviceCount)
}
```

---

### Step 5.6 – Generate Compliance Report in Intune Portal

1. In Intune admin center: **Reports > Device compliance > Reports tab**.
2. Click **Generate report** for **Device compliance org**.
3. Wait for the report to generate (typically 1–5 minutes).
4. Download the CSV; open in Excel and create a pivot table:
   - Rows: **Compliance state**
   - Values: **Count of Device name**
   - Filter: **OS**
5. Create a bar chart from the pivot table to visualize compliance by platform.

---

### Step 5.7 – Endpoint Analytics Dashboard

1. In Intune admin center: **Reports > Endpoint analytics**.
2. Review the **Startup performance** score:
   - Identify devices with scores below 50 (poor startup experience).
   - Check the **Startup processes** report to identify boot-time software.
3. Review the **Work from anywhere** score — this composite score reflects management coverage and health.
4. Review **Application reliability** — note any apps with high crash rates.
5. Export the **Startup performance** report to CSV for the Contoso IT manager.

---

### Step 5.8 – Configure a Custom Compliance Notification Email

1. In Intune admin center: **Tenant administration > Customization**.
2. Upload Contoso's logo and company name for branded notifications.
3. In **Devices > Compliance policies > Notifications > Create notification**:
   - Name: `Contoso-NonCompliance-EmailTemplate`
   - Email header: include company logo
   - Subject: `Action Required: Your Device is Not Compliant`
   - Body: Include instructions for common remediation steps (BitLocker, OS update, password).
4. Assign the notification to the **Actions for noncompliance** section of the Windows compliance policy (Day 3 action).

---

### Step 5.9 – Create a Power Automate Flow for Compliance Alerts (Optional)

1. Sign in to **Power Automate** (`make.powerautomate.com`).
2. Create a new **Automated cloud flow**.
3. Trigger: **Microsoft Dataverse – When a row is added, modified, or deleted** (if Intune data is synced to Dataverse via Power Platform connector) — *or* use a **Scheduled flow** with an HTTP action calling the Graph API.
4. Action: Send an **Adaptive Card** to a Teams channel (*Contoso-ITOps*) with a summary of new non-compliant devices.
5. This provides the security team real-time visibility without needing to open the Intune portal.

> Full Power Automate configuration is beyond the scope of this lab; this step outlines the architecture for reference.

---

### Phase 5 Verification Checklist

- [ ] Microsoft Graph PowerShell SDK installed and authenticated successfully
- [ ] `Get-NonCompliantDevices.ps1` runs without error; CSV output contains device data
- [ ] Bulk sync script runs without unhandled errors
- [ ] App installation summary script returns data for at least one app
- [ ] Device compliance org report generated and downloaded from Intune portal
- [ ] Endpoint Analytics dashboard reviewed; startup performance scores noted
- [ ] Custom compliance notification email template created and assigned
- [ ] (Optional) Power Automate flow designed for compliance alerting

---

## Overall Capstone Verification Checklist

### Infrastructure
- [ ] Tenant MDM authority confirmed as Microsoft Intune
- [ ] All prerequisite connectors active (APNs, Managed Google Play, Autopilot)
- [ ] Entra ID groups created and populated

### Enrollment
- [ ] At least one Windows device enrolled via Autopilot and visible in Devices > All devices
- [ ] At least one iOS device enrolled
- [ ] At least one Android device enrolled with work profile

### Applications
- [ ] Win32 app deployed as Required; shows Installed
- [ ] Microsoft 365 Apps deployed to Windows devices
- [ ] App protection policy active on iOS; data transfer restrictions validated

### Security & Compliance
- [ ] Windows compliance policy active; test device shows Compliant
- [ ] Security baseline deployed with no critical conflicts
- [ ] Conditional access policy enforcing compliance requirement (On mode)
- [ ] Legacy auth blocked by CA policy

### Automation & Reporting
- [ ] Non-compliant device export script produces valid CSV output
- [ ] Intune compliance report downloaded
- [ ] Endpoint Analytics reviewed

---

## Troubleshooting Reference

| Symptom | First Check | Common Fix |
|---|---|---|
| Device stuck at ESP | IME log `%ProgramData%\Microsoft\IntuneManagementExtension\Logs` | Increase ESP timeout; identify blocking app |
| Win32 app pending install | IME log; look for error codes | Check install command, run as SYSTEM, re-wrap .intunewin |
| iOS profile not deploying | APNs cert expiry; assignment filter | Renew APNs; fix filter rule |
| Non-compliant despite BitLocker on | Compliance policy encryption method | Switch to Intune-reported encryption check |
| Conditional access blocking compliant device | Sign-in logs > CA tab | Fix group membership; use What If tool |
| Android enrollment fails | Google Play binding status | Re-bind Managed Google Play |
| Graph script auth error | Delegated vs application permissions | Add required scopes to Connect-MgGraph |
| SCEP cert pending | NDES reachability | Verify NDES URL, cert connector service running |

---

*End of Lab Guide — Module 11 Capstone*

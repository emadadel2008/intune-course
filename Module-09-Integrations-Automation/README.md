# Module 09: Integrations & Advanced Automation

## Module Overview

This module explores how Microsoft Intune integrates with the broader Microsoft 365 and Azure ecosystem to deliver end-to-end endpoint management at scale. You will learn how to streamline device provisioning with Windows Autopilot, enforce data governance through Microsoft Purview, and automate repetitive management tasks using PowerShell with the Microsoft Graph SDK, raw Graph API calls, and cloud-based automation tools such as Azure Automation, Logic Apps, and Power Automate. By the end of this module you will be capable of designing resilient, zero-touch workflows that reduce manual effort and ensure consistent policy enforcement across your entire device fleet.

---

## Learning Objectives

By the end of this module, you will be able to:

- Configure Windows Autopilot profiles and deployment modes for zero-touch device provisioning
- Walk through the Out-of-Box Experience (OOBE) and validate Autopilot-driven enrollment
- Integrate Microsoft Purview Data Loss Prevention (DLP) policies and sensitivity labels with Intune Mobile Application Management (MAM)
- Install and use the Microsoft Graph PowerShell SDK to query and manage Intune objects
- Execute authenticated REST calls against the Microsoft Graph API using Graph Explorer and scripted HTTP requests
- Build batch Graph API requests to perform bulk operations efficiently
- Design automation patterns using Azure Automation runbooks, Logic Apps, and Power Automate flows triggered by Intune events
- Troubleshoot common integration and automation failures

---

## Key Topics

### 1. Windows Autopilot

Windows Autopilot transforms the device deployment experience by pre-configuring Windows devices in the cloud so that end users receive a fully managed, policy-compliant device with minimal IT interaction.

#### Autopilot Profiles

An Autopilot deployment profile defines how a device is set up during OOBE. Key profile settings include:

| Setting | Description |
|---|---|
| Deployment mode | Self-Deploying or User-Driven |
| Join type | Azure AD Join or Hybrid Azure AD Join |
| User account type | Standard or Administrator |
| Skip EULA | Bypass the End-User License Agreement screen |
| Skip privacy settings | Hide the privacy settings page |
| Hide change account options | Prevent users from changing the account used during setup |
| Apply device name template | Auto-generate names (e.g., `CORP-%SERIAL%`) |

Profiles are created in **Intune admin center → Devices → Enrollment → Windows Autopilot → Deployment profiles** and assigned to Azure AD device groups containing the registered hardware hashes.

#### Deployment Modes

**User-Driven Mode**  
The most common mode. The device is associated with a specific user. The user authenticates during OOBE and the device is Azure AD–joined and enrolled automatically. Suitable for corporate-owned or personal devices that will be assigned to individuals.

**Self-Deploying Mode**  
Designed for shared devices, kiosks, and digital signage. Requires TPM 2.0 with device attestation. No user interaction is required — the device enrolls, receives all policies and apps, and presents a ready-to-use desktop. Uses device-based Azure AD join without user affinity.

**Pre-provisioning (White Glove)**  
Allows IT or partners to run the device-specific portion of OOBE in advance. The end user then completes only the user-specific phase, dramatically reducing setup time on-site.

#### OOBE Flow

1. Device powers on and connects to network
2. Windows contacts the Autopilot service using the hardware hash
3. Autopilot profile is downloaded and OOBE pages are suppressed per profile settings
4. Device joins Azure AD (or hybrid AD) and enrolls in Intune
5. Enrollment Status Page (ESP) tracks app and policy installation progress
6. User receives a fully configured desktop

#### Enrollment Status Page (ESP)

The ESP provides real-time progress during OOBE. Configure it under **Devices → Enrollment → Windows → Enrollment Status Page**. Best practice is to block device use until required apps and policies are applied to prevent users accessing an incompletely configured machine.

---

### 2. Microsoft Purview Integration

Microsoft Purview (formerly Microsoft Information Protection and Microsoft Compliance) provides data governance capabilities that complement Intune's device and app management.

#### Data Loss Prevention (DLP)

DLP policies detect and prevent the sharing of sensitive information such as credit card numbers, social security numbers, or health records. When integrated with Intune:

- **Endpoint DLP** policies can restrict copy, paste, upload, print, and share operations on managed Windows 10/11 devices
- Policies are created in the **Microsoft Purview compliance portal** under **Data loss prevention → Policies**
- Intune device compliance state can be used as a condition in DLP policy scopes (e.g., only enforce stricter rules on non-compliant devices)

#### Sensitivity Labels

Sensitivity labels classify and optionally protect content (documents, emails, meetings). Integration with Intune MAM allows:

- Requiring a minimum sensitivity label before allowing file open in managed apps
- Blocking download of highly confidential content to unmanaged devices via Conditional Access + App Protection Policies
- Auto-labeling via Purview policies that detect sensitive data patterns

Labels are published through **Purview compliance portal → Information protection → Labels** and consumed by Microsoft 365 Apps, Edge, and mobile Office apps enrolled via Intune MAM.

#### Information Protection Policies in Intune

Under **Apps → App protection policies**, you can configure:

- **Data transfer restrictions** — prevent "save as" to personal cloud storage
- **Managed browser** — enforce Intune-managed Edge for web links from managed apps
- **Screen capture block** — prevent screenshots in managed apps on Android
- **Encryption** — enforce OS-level or SDK-level encryption for app data at rest

Combining Purview sensitivity labels with Intune MAM creates a layered data governance model that protects data regardless of whether the action occurs on a managed device or an unmanaged BYOD device.

---

### 3. PowerShell with Microsoft Graph SDK

The **Microsoft Graph PowerShell SDK** provides cmdlets that map directly to Graph API endpoints, making it easier to script Intune management tasks without constructing raw HTTP requests.

#### Installation

```powershell
# Install the SDK (requires PowerShell 5.1+ or PowerShell 7+)
Install-Module Microsoft.Graph -Scope CurrentUser -Force

# Install only the Intune-specific sub-module to reduce footprint
Install-Module Microsoft.Graph.DeviceManagement -Scope CurrentUser -Force
```

#### Authentication

```powershell
# Interactive (delegated) login — prompts browser-based MFA
Connect-MgGraph -Scopes "DeviceManagementManagedDevices.ReadWrite.All",
                         "DeviceManagementConfiguration.ReadWrite.All",
                         "DeviceManagementApps.ReadWrite.All",
                         "User.Read.All"

# Verify the connected context
Get-MgContext
```

For unattended scripts use a service principal with a client secret or certificate:

```powershell
$tenantId     = "<YOUR_TENANT_ID>"
$clientId     = "<YOUR_APP_CLIENT_ID>"
$clientSecret = "<YOUR_CLIENT_SECRET>"

$secureSecret = ConvertTo-SecureString $clientSecret -AsPlainText -Force
$credential   = New-Object System.Management.Automation.PSCredential($clientId, $secureSecret)

Connect-MgGraph -TenantId $tenantId -ClientSecretCredential $credential
```

#### Common Management Operations

```powershell
# List all managed devices
Get-MgDeviceManagementManagedDevice | Select-Object DeviceName, OperatingSystem, ComplianceState

# Filter devices by OS
Get-MgDeviceManagementManagedDevice -Filter "operatingSystem eq 'Windows'" |
    Select-Object DeviceName, OsVersion, LastSyncDateTime

# Trigger a sync on a specific device
$deviceId = "<DEVICE_OBJECT_ID>"
Sync-MgDeviceManagementManagedDevice -ManagedDeviceId $deviceId

# Get all configuration policies
Get-MgDeviceManagementDeviceConfiguration | Select-Object DisplayName, Id, LastModifiedDateTime

# Get compliance policies
Get-MgDeviceManagementDeviceCompliancePolicy | Select-Object DisplayName, Id

# Assign a compliance policy to a group
$policyId = "<COMPLIANCE_POLICY_ID>"
$groupId  = "<AAD_GROUP_ID>"

$assignment = @{
    target = @{
        "@odata.type" = "#microsoft.graph.groupAssignmentTarget"
        groupId       = $groupId
    }
}

New-MgDeviceManagementDeviceCompliancePolicyAssignment `
    -DeviceCompliancePolicyId $policyId `
    -BodyParameter $assignment
```

#### Useful Helper Functions

```powershell
function Get-AllMgPages {
    <#
    .SYNOPSIS
        Retrieves all pages of results from a Microsoft Graph cmdlet call.
    .EXAMPLE
        Get-AllMgPages -ScriptBlock { Get-MgDeviceManagementManagedDevice -All }
    #>
    param([scriptblock]$ScriptBlock)
    & $ScriptBlock
}

function Invoke-MgRetry {
    <#
    .SYNOPSIS
        Wraps a Graph SDK call with exponential back-off for throttling resilience.
    #>
    param(
        [scriptblock]$ScriptBlock,
        [int]$MaxRetries = 5
    )
    $attempt = 0
    do {
        try {
            return & $ScriptBlock
        }
        catch {
            if ($_.Exception.Message -match '429|throttl') {
                $wait = [math]::Pow(2, $attempt)
                Write-Warning "Throttled. Retrying in $wait seconds..."
                Start-Sleep -Seconds $wait
                $attempt++
            } else {
                throw
            }
        }
    } while ($attempt -le $MaxRetries)
    throw "Max retries ($MaxRetries) exceeded."
}
```

#### Disconnect

```powershell
Disconnect-MgGraph
```

---

### 4. Microsoft Graph API

The Microsoft Graph API is the unified REST endpoint (`https://graph.microsoft.com`) for all Microsoft 365 services including Intune. Understanding it directly lets you build integrations in any language and troubleshoot SDK behaviour.

#### Authentication

Graph API uses OAuth 2.0. Obtain a token using the client credentials flow:

```http
POST https://login.microsoftonline.com/{tenant-id}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials
&client_id={client-id}
&client_secret={client-secret}
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
```

Use the returned `access_token` as a `Bearer` token in subsequent requests.

#### Key Intune Endpoints

| Resource | Endpoint |
|---|---|
| Managed devices | `GET /v1.0/deviceManagement/managedDevices` |
| Device configurations | `GET /v1.0/deviceManagement/deviceConfigurations` |
| Compliance policies | `GET /v1.0/deviceManagement/deviceCompliancePolicies` |
| App protection policies | `GET /v1.0/deviceManagement/managedAppPolicies` |
| Enrolled users | `GET /v1.0/users` |
| Autopilot devices | `GET /v1.0/deviceManagement/windowsAutopilotDeviceIdentities` |

#### Query Parameters

```http
# Select specific fields
GET /v1.0/deviceManagement/managedDevices?$select=deviceName,operatingSystem,complianceState

# Filter results
GET /v1.0/deviceManagement/managedDevices?$filter=operatingSystem eq 'Windows'

# Sort results
GET /v1.0/deviceManagement/managedDevices?$orderby=lastSyncDateTime desc

# Pagination
GET /v1.0/deviceManagement/managedDevices?$top=25&$skip=25
```

#### Batch Requests

Batch up to 20 requests into a single HTTP call to improve performance:

```http
POST https://graph.microsoft.com/v1.0/$batch
Content-Type: application/json
Authorization: Bearer {token}

{
  "requests": [
    {
      "id": "1",
      "method": "GET",
      "url": "/deviceManagement/managedDevices?$top=5"
    },
    {
      "id": "2",
      "method": "GET",
      "url": "/deviceManagement/deviceCompliancePolicies"
    },
    {
      "id": "3",
      "method": "GET",
      "url": "/deviceManagement/deviceConfigurations"
    }
  ]
}
```

---

### 5. Automation Patterns

#### Azure Automation Runbooks

Azure Automation runbooks are PowerShell or Python scripts that run on a schedule or in response to webhooks. Typical Intune automation patterns include:

- **Nightly compliance report** — export non-compliant devices to a storage account or send via email
- **Stale device cleanup** — retire devices that have not synced in 90+ days
- **Dynamic group refresh** — invoke Graph API to ensure group membership reflects current device attributes

Runbooks use a **Managed Identity** or **Run As Account** with the appropriate Graph API application permissions.

#### Logic Apps

Azure Logic Apps provide a low-code integration platform. Common Intune patterns:

- Trigger on a new row in SharePoint (device request form) → call Graph API to pre-register Autopilot device → send confirmation email
- Monitor a storage queue for CSV files → parse hardware hashes → bulk-import to Autopilot
- Trigger on Teams message matching a keyword → initiate remote wipe via Graph API

#### Power Automate

Power Automate is the Microsoft 365–native automation platform. Useful for:

- HR-driven onboarding — when a new employee record is created in HR system, add the user to the correct Intune enrollment group
- Helpdesk flows — a Teams Adaptive Card lets helpdesk staff trigger device sync or lock without needing Intune portal access
- Approval workflows — require manager approval before a device wipe is executed

#### Webhook-Driven Automation

Intune can send compliance state change notifications to an **Azure Event Hub** or via **Endpoint analytics** export. These events can trigger Logic Apps or Azure Functions that automatically open tickets in ServiceNow or JIRA.

#### Scheduling Considerations

When scheduling automation runbooks that interact with Intune via the Graph API, keep the following in mind:

- **Throttling limits:** The Graph API enforces per-app and per-tenant rate limits. For bulk operations, stagger jobs across time windows and use batch requests where possible.
- **Token lifetime:** OAuth tokens issued by the client credentials flow are valid for 3,600 seconds (1 hour). Long-running scripts must refresh tokens proactively rather than waiting for a 401 error.
- **Idempotency:** Design automation so that running the same script twice produces the same result without creating duplicate objects. Use `GET` before `POST`/`PATCH` to check for existing resources.
- **Error handling:** Always wrap Graph calls in `try/catch` blocks and log failures to a persistent store (Log Analytics, storage table) so that partial failures are visible and recoverable.

---

## Hands-On Labs

### Lab 9.1: Configure Windows Autopilot Profile

**Objective:** Register a device in Windows Autopilot, create a deployment profile, assign it to a device group, and walk through the expected OOBE experience.

**Prerequisites:** Global Administrator or Intune Administrator role; a test Windows 10/11 device or VM with network connectivity.

**Steps:**

1. **Capture the hardware hash.** On the target device, open PowerShell as Administrator and run:
   ```powershell
   Install-Script -Name Get-WindowsAutoPilotInfo -Force
   Get-WindowsAutoPilotInfo -OutputFile C:\AutopilotHWID.csv
   ```
   Copy `C:\AutopilotHWID.csv` to an accessible location.

2. **Import the hardware hash into Intune.** Navigate to **Intune admin center → Devices → Enrollment → Windows → Windows Autopilot → Devices**. Click **Import**, browse to `AutopilotHWID.csv`, and click **Import**. Wait for the import to complete (this can take up to 15 minutes).

3. **Create an Autopilot deployment profile.** Go to **Devices → Enrollment → Windows → Deployment profiles → Create profile → Windows PC**. Name the profile `Lab-Autopilot-UserDriven`. Set:
   - Deployment mode: **User-Driven**
   - Join to Azure AD as: **Azure AD joined**
   - User account type: **Standard User**
   - Apply device name template: **Yes** → `CORP-%SERIAL%`
   - Skip EULA: **Yes**
   - Skip privacy settings: **Yes**

4. **Configure the Enrollment Status Page (ESP).** Under **Devices → Enrollment → Windows → Enrollment Status Page**, create a new profile named `Lab-ESP`. Enable **Show app and profile configuration progress**. Block device use until all profiles and apps are installed. Add critical apps to the blocking app list.

5. **Create a dynamic Azure AD device group.** In **Azure AD (Entra ID) → Groups → New group**, create a security group named `Autopilot-Lab-Devices` with membership type **Dynamic Device**. Set the dynamic rule:
   ```
   (device.devicePhysicalIds -any (_ -startsWith "[ZTDId]"))
   ```
   This automatically includes all Autopilot-registered devices.

6. **Assign the profile to the group.** Return to the Autopilot deployment profile `Lab-Autopilot-UserDriven`, click **Assignments**, and add the `Autopilot-Lab-Devices` group.

7. **Assign the ESP profile to the group.** Return to the ESP profile `Lab-ESP` and assign it to `Autopilot-Lab-Devices`.

8. **Reset or factory-reset the test device.** On the test device go to **Settings → System → Recovery → Reset this PC → Remove everything** or use a fresh OS image. The device must start OOBE fresh.

9. **Observe the OOBE experience.** Power on the device and connect to a corporate or internet network. Observe that the custom OOBE screens appear (or are suppressed), the ESP progress page is shown, and the device is automatically named using the template.

10. **Verify enrollment.** After setup completes, confirm the device appears in **Intune → Devices → All devices** with the correct name and compliance state.

**Expected Outcome:** The device completes OOBE with minimal user interaction, joins Azure AD automatically, and appears in Intune with the assigned policies and name template applied.

---

### Lab 9.2: Integrate with Microsoft Purview

**Objective:** Configure sensitivity labels in Microsoft Purview and link them to Intune App Protection Policies to enforce data governance on managed mobile apps.

**Prerequisites:** Microsoft 365 E3 or E5 license; Compliance Administrator or Global Administrator role; at least one iOS or Android device enrolled in Intune MAM.

**Steps:**

1. **Access the Purview compliance portal.** Navigate to [compliance.microsoft.com](https://compliance.microsoft.com) and sign in with your administrator account.

2. **Create a sensitivity label.** Go to **Information protection → Labels → Create a label**. Name it `Confidential - Intune Managed`. Set:
   - Scope: **Files & emails** and **Groups & sites**
   - Encryption: **Apply encryption** → Assign permissions now → Add `authenticated users` with **Reviewer** rights
   - Content marking: Add a header `CONFIDENTIAL — Internal Use Only`

3. **Publish the label.** Go to **Label policies → Publish label**. Select the `Confidential - Intune Managed` label. Target the policy to the `All Users` group. Name the policy `Intune-Purview-Label-Policy`.

4. **Enable endpoint DLP.** Navigate to **Data loss prevention → Policies → Create policy**. Choose **Custom policy**. Name it `Lab-Endpoint-DLP`. Set location to **Devices** (Windows 10/11 endpoints onboarded to Defender for Endpoint/Intune). Add a rule that detects credit card numbers and blocks **Upload to cloud** and **Copy to clipboard** when the sensitivity label is `Confidential - Intune Managed`.

5. **Configure an Intune App Protection Policy for iOS.** In the Intune admin center, go to **Apps → App protection policies → Create policy → iOS/iPadOS**. Name it `Purview-MAM-iOS`. Under **Data protection**:
   - Restrict cut, copy, paste to **Policy managed apps**
   - Save copies of org data: **Block**
   - Receive data from other apps: **Policy managed apps only**

6. **Link the label requirement.** Under **Data protection → Minimum required sensitivity label**, set the minimum label to `Confidential - Intune Managed`. This prevents users from opening files labelled above this threshold in unmanaged apps.

7. **Assign the App Protection Policy.** Assign `Purview-MAM-iOS` to a test user group containing your enrolled iOS test users.

8. **Test the policy.** On an enrolled iOS device, open the Outlook app and attempt to forward an email containing a file labelled `Confidential - Intune Managed` to a personal Gmail address. Verify the action is blocked and a policy tip is displayed.

**Expected Outcome:** Sensitivity labels created in Purview are honoured by Intune-managed apps, and attempts to exfiltrate labelled data are blocked according to the App Protection Policy settings.

---

### Lab 9.3: Use PowerShell for Intune Management

**Objective:** Install the Microsoft Graph PowerShell SDK, authenticate using delegated permissions, list managed devices, and assign a compliance policy to a group using PowerShell.

**Prerequisites:** PowerShell 5.1 or PowerShell 7; Intune Administrator role; Azure AD group and compliance policy already created.

**Steps:**

1. **Open PowerShell as Administrator** and verify the execution policy allows script execution:
   ```powershell
   Get-ExecutionPolicy
   # If it returns Restricted, run:
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
   ```

2. **Install the Microsoft Graph SDK modules:**
   ```powershell
   Install-Module Microsoft.Graph -Scope CurrentUser -Force -AllowClobber
   Install-Module Microsoft.Graph.DeviceManagement -Scope CurrentUser -Force -AllowClobber
   Write-Host "Modules installed successfully." -ForegroundColor Green
   ```

3. **Authenticate interactively with the required scopes:**
   ```powershell
   Connect-MgGraph -Scopes `
       "DeviceManagementManagedDevices.ReadWrite.All",
       "DeviceManagementConfiguration.ReadWrite.All",
       "DeviceManagementApps.ReadWrite.All",
       "User.Read.All",
       "Group.Read.All"

   # Confirm the signed-in account and tenant
   $ctx = Get-MgContext
   Write-Host "Connected as $($ctx.Account) to tenant $($ctx.TenantId)"
   ```

4. **List all managed devices and export to CSV:**
   ```powershell
   $devices = Get-MgDeviceManagementManagedDevice -All |
       Select-Object DeviceName,
                     OperatingSystem,
                     OsVersion,
                     ComplianceState,
                     LastSyncDateTime,
                     UserPrincipalName

   $devices | Format-Table -AutoSize

   # Export results
   $devices | Export-Csv -Path ".\ManagedDevices.csv" -NoTypeInformation
   Write-Host "Exported $($devices.Count) devices to ManagedDevices.csv"
   ```

5. **List all compliance policies:**
   ```powershell
   $policies = Get-MgDeviceManagementDeviceCompliancePolicy |
       Select-Object DisplayName, Id, LastModifiedDateTime

   $policies | Format-Table -AutoSize
   ```

6. **Capture the policy ID and group ID for assignment:**
   ```powershell
   # Replace display name with your policy name
   $policyName = "Windows 10 Compliance"
   $policy = Get-MgDeviceManagementDeviceCompliancePolicy |
       Where-Object { $_.DisplayName -eq $policyName }

   $policyId = $policy.Id
   Write-Host "Policy ID: $policyId"

   # Find the target group
   $groupName = "Autopilot-Lab-Devices"
   $group = Get-MgGroup -Filter "displayName eq '$groupName'"
   $groupId = $group.Id
   Write-Host "Group ID: $groupId"
   ```

7. **Assign the compliance policy to the group:**
   ```powershell
   $assignmentBody = @{
       target = @{
           "@odata.type" = "#microsoft.graph.groupAssignmentTarget"
           groupId       = $groupId
       }
   }

   New-MgDeviceManagementDeviceCompliancePolicyAssignment `
       -DeviceCompliancePolicyId $policyId `
       -BodyParameter $assignmentBody

   Write-Host "Policy '$policyName' assigned to group '$groupName' successfully." -ForegroundColor Green
   ```

8. **Verify the assignment:**
   ```powershell
   Get-MgDeviceManagementDeviceCompliancePolicyAssignment `
       -DeviceCompliancePolicyId $policyId |
       Select-Object Id, @{N='Target';E={$_.Target.AdditionalProperties.groupId}}
   ```

9. **Disconnect when finished:**
   ```powershell
   Disconnect-MgGraph
   Write-Host "Disconnected from Microsoft Graph."
   ```

**Expected Outcome:** The compliance policy is successfully assigned to the target Azure AD group via PowerShell, and the assignment is visible in the Intune admin center under the policy's **Properties → Assignments** blade.

---

### Lab 9.4: Use Microsoft Graph API

**Objective:** Explore the Microsoft Graph API using Graph Explorer, then construct and execute authenticated REST calls to query and manage Intune device data.

**Prerequisites:** A browser with access to [developer.microsoft.com/en-us/graph/graph-explorer](https://developer.microsoft.com/en-us/graph/graph-explorer); Intune Administrator role; PowerShell or a tool like `curl`/Postman for raw HTTP requests.

**Steps:**

1. **Open Graph Explorer.** Navigate to [https://aka.ms/ge](https://aka.ms/ge) and sign in with your Intune administrator account. Consent to the requested permissions when prompted.

2. **Run a basic managed devices query.** In the query bar, enter:
   ```
   GET https://graph.microsoft.com/v1.0/deviceManagement/managedDevices
   ```
   Click **Run query**. Review the JSON response containing your enrolled devices.

3. **Apply OData query parameters.** Modify the URL to filter and select fields:
   ```
   GET https://graph.microsoft.com/v1.0/deviceManagement/managedDevices?$select=deviceName,operatingSystem,complianceState&$filter=operatingSystem eq 'Windows'&$orderby=lastSyncDateTime desc&$top=10
   ```

4. **Obtain an access token using PowerShell** for scripted API calls:
   ```powershell
   $tenantId     = "<YOUR_TENANT_ID>"
   $clientId     = "<YOUR_APP_CLIENT_ID>"
   $clientSecret = "<YOUR_CLIENT_SECRET>"

   $tokenUrl = "https://login.microsoftonline.com/$tenantId/oauth2/v2.0/token"

   $body = @{
       grant_type    = "client_credentials"
       client_id     = $clientId
       client_secret = $clientSecret
       scope         = "https://graph.microsoft.com/.default"
   }

   $tokenResponse = Invoke-RestMethod -Method POST -Uri $tokenUrl -Body $body
   $accessToken   = $tokenResponse.access_token
   Write-Host "Access token acquired (length: $($accessToken.Length))"
   ```

5. **Call the Graph API to list managed devices:**
   ```powershell
   $headers = @{
       Authorization  = "Bearer $accessToken"
       "Content-Type" = "application/json"
   }

   $uri      = "https://graph.microsoft.com/v1.0/deviceManagement/managedDevices?`$top=5"
   $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

   $response.value | Select-Object deviceName, operatingSystem, complianceState | Format-Table
   ```

6. **Trigger a remote device sync via Graph API:**
   ```powershell
   # Replace with the actual managed device ID from previous response
   $deviceId  = "<MANAGED_DEVICE_ID>"
   $syncUri   = "https://graph.microsoft.com/v1.0/deviceManagement/managedDevices/$deviceId/syncDevice"

   Invoke-RestMethod -Method POST -Uri $syncUri -Headers $headers
   Write-Host "Sync command sent to device $deviceId"
   ```

7. **Send a batch request to retrieve multiple resources in one call:**
   ```powershell
   $batchUri  = "https://graph.microsoft.com/v1.0/`$batch"

   $batchBody = @{
       requests = @(
           @{ id = "1"; method = "GET"; url = "/deviceManagement/managedDevices?`$top=3" },
           @{ id = "2"; method = "GET"; url = "/deviceManagement/deviceCompliancePolicies" },
           @{ id = "3"; method = "GET"; url = "/deviceManagement/deviceConfigurations" }
       )
   } | ConvertTo-Json -Depth 5

   $batchResponse = Invoke-RestMethod -Method POST -Uri $batchUri -Headers $headers -Body $batchBody
   $batchResponse.responses | ForEach-Object {
       Write-Host "Request $($_.id) returned status $($_.status)"
   }
   ```

8. **Explore permissions using Graph Explorer.** In Graph Explorer, click **Modify permissions** and review which permissions are needed for write operations such as `DeviceManagementManagedDevices.PrivilegedOperations.All`. Note which permissions require admin consent.

**Expected Outcome:** You can construct and send authenticated Graph API requests, filter and select data with OData parameters, trigger device management actions, and batch multiple calls for efficiency.

---

### Lab 9.5: Automation Script for Device Enrollment

**Objective:** Write a PowerShell script that reads device hardware hashes from a CSV file, bulk-imports them into Windows Autopilot, and automatically assigns a deployment profile to each new device group.

**Prerequisites:** PowerShell 7+; `WindowsAutopilotIntune` and `Microsoft.Graph` modules installed; Intune Administrator role; a CSV file containing hardware hashes.

**Steps:**

1. **Create a sample hardware hash CSV** (in a real scenario this is exported from devices; here we create a placeholder structure):
   ```powershell
   # The real CSV from Get-WindowsAutoPilotInfo has these columns:
   # Device Serial Number, Windows Product ID, Hardware Hash, Group Tag, Assigned User

   # Verify the format of your existing CSV
   $csv = Import-Csv -Path ".\AutopilotDevices.csv"
   $csv | Select-Object -First 3 | Format-Table
   ```

2. **Install required modules:**
   ```powershell
   Install-Module WindowsAutopilotIntune -Scope CurrentUser -Force
   Install-Module Microsoft.Graph.DeviceManagement -Scope CurrentUser -Force
   Write-Host "Modules ready."
   ```

3. **Define the automation script — save as `Invoke-AutopilotBulkEnroll.ps1`:**
   ```powershell
   #Requires -Modules Microsoft.Graph.DeviceManagement, WindowsAutopilotIntune
   <#
   .SYNOPSIS
       Bulk-import Autopilot devices and assign a deployment profile.
   .PARAMETER CsvPath
       Path to the hardware hash CSV file.
   .PARAMETER ProfileName
       Display name of the Autopilot deployment profile to assign.
   .PARAMETER GroupName
       Display name of the Azure AD group to add new devices into.
   #>
   param(
       [Parameter(Mandatory)][string]$CsvPath,
       [Parameter(Mandatory)][string]$ProfileName,
       [Parameter(Mandatory)][string]$GroupName
   )

   Set-StrictMode -Version Latest
   $ErrorActionPreference = "Stop"

   # ── Authentication ──────────────────────────────────────────────────────────
   Write-Host "[1/5] Connecting to Microsoft Graph..." -ForegroundColor Cyan
   Connect-MgGraph -Scopes `
       "DeviceManagementServiceConfig.ReadWrite.All",
       "DeviceManagementManagedDevices.ReadWrite.All",
       "Group.ReadWrite.All"

   # ── Import hardware hashes ───────────────────────────────────────────────────
   Write-Host "[2/5] Importing hardware hashes from $CsvPath..." -ForegroundColor Cyan
   $devices = Import-Csv -Path $CsvPath

   if ($devices.Count -eq 0) {
       Write-Error "CSV file is empty or malformed. Exiting."
   }

   foreach ($device in $devices) {
       $importBody = @{
           "@odata.type"        = "#microsoft.graph.importedWindowsAutopilotDeviceIdentity"
           serialNumber         = $device.'Device Serial Number'
           productKey           = $device.'Windows Product ID'
           hardwareIdentifier   = $device.'Hardware Hash'
           groupTag             = $device.'Group Tag'
           assignedUserPrincipalName = $device.'Assigned User'
       }

       try {
           New-MgDeviceManagementImportedWindowsAutopilotDeviceIdentity `
               -BodyParameter $importBody | Out-Null
           Write-Host "  Imported: $($device.'Device Serial Number')" -ForegroundColor Green
       }
       catch {
           Write-Warning "  Failed to import $($device.'Device Serial Number'): $_"
       }
   }

   # ── Wait for import to process ───────────────────────────────────────────────
   Write-Host "[3/5] Waiting 60 seconds for import to process..." -ForegroundColor Cyan
   Start-Sleep -Seconds 60

   # ── Locate the deployment profile ───────────────────────────────────────────
   Write-Host "[4/5] Resolving deployment profile '$ProfileName'..." -ForegroundColor Cyan
   $profile = Get-MgDeviceManagementWindowsAutopilotDeploymentProfile |
       Where-Object { $_.DisplayName -eq $ProfileName }

   if (-not $profile) {
       Write-Error "Autopilot profile '$ProfileName' not found."
   }

   # ── Assign profile to group ──────────────────────────────────────────────────
   Write-Host "[5/5] Assigning profile to group '$GroupName'..." -ForegroundColor Cyan
   $group = Get-MgGroup -Filter "displayName eq '$GroupName'"

   if (-not $group) {
       Write-Error "Azure AD group '$GroupName' not found."
   }

   $assignBody = @{
       target = @{
           "@odata.type" = "#microsoft.graph.groupAssignmentTarget"
           groupId       = $group.Id
       }
   }

   New-MgDeviceManagementWindowsAutopilotDeploymentProfileAssignment `
       -WindowsAutopilotDeploymentProfileId $profile.Id `
       -BodyParameter $assignBody

   Write-Host "`nDone! Profile '$ProfileName' assigned to group '$GroupName'." -ForegroundColor Green
   Disconnect-MgGraph
   ```

4. **Review the script** and substitute your real profile and group names.

5. **Run the script:**
   ```powershell
   .\Invoke-AutopilotBulkEnroll.ps1 `
       -CsvPath        ".\AutopilotDevices.csv" `
       -ProfileName    "Lab-Autopilot-UserDriven" `
       -GroupName      "Autopilot-Lab-Devices"
   ```

6. **Monitor the import status.** Navigate to **Intune admin center → Devices → Enrollment → Windows → Autopilot → Devices** and refresh. Newly imported devices should appear with a **Pending** or **Assigned** state.

7. **Verify profile assignment.** Go to **Devices → Enrollment → Windows → Deployment profiles → Lab-Autopilot-UserDriven → Properties → Assignments** and confirm the group is listed.

8. **Test with a virtual machine.** Use a Hyper-V VM with the captured hardware hash to simulate a fresh OOBE and confirm the profile is applied automatically.

**Expected Outcome:** The script successfully imports all devices from the CSV, waits for processing, and assigns the specified Autopilot deployment profile to the group — all without manual portal interaction.

---

## Best Practices

### Do's ✅

- **Use Managed Identities** instead of service principals with static client secrets for Azure Automation runbooks and Logic Apps — eliminates credential rotation overhead
- **Apply least-privilege permissions** when registering Graph API app registrations; request only the scopes your script actually uses
- **Use the Enrollment Status Page** to block device use until all required apps and configurations are applied, preventing users accessing a partially configured device
- **Tag Autopilot devices** with group tags to allow dynamic Azure AD group rules to target subsets of devices (e.g., by department or region)
- **Version-control all automation scripts** in a Git repository and use pull request reviews before deploying changes that affect production enrollments
- **Log automation output** to Azure Monitor, Log Analytics, or a storage account for audit trails and troubleshooting
- **Test Autopilot profiles in a pilot group** before assigning to all devices — a misconfigured profile can render devices unusable until manually reset
- **Paginate Graph API results** using `@odata.nextLink` to ensure all records are retrieved when result sets exceed the page size limit (default 100 for many endpoints)
- **Use `$select` in Graph queries** to request only the fields you need, improving performance and reducing token size

### Don'ts ❌

- ❌ **Don't hardcode credentials** (client secrets, passwords) in PowerShell scripts or Logic App definitions — use Azure Key Vault references instead
- ❌ **Don't use Global Administrator** for day-to-day automation service accounts — the Intune Service Administrator or a custom role with minimum required permissions is sufficient
- ❌ **Don't bypass the ESP** (Enrollment Status Page) in production environments — doing so risks users logging in before security policies are applied
- ❌ **Don't run bulk device operations** (wipe, retire, reset) in automation without a dry-run mode that logs intended actions before executing them
- ❌ **Don't rely on device display names** as unique identifiers in scripts — always use immutable object IDs from Azure AD or Intune
- ❌ **Don't ignore throttling responses** — Graph API returns `HTTP 429 Too Many Requests`; implement exponential backoff and honour the `Retry-After` header
- ❌ **Don't import Autopilot hardware hashes manually in production** — automate the process to reduce transcription errors and maintain consistency
- ❌ **Don't skip testing Purview label policies** in a pilot scope; publishing labels too broadly before validating can disrupt user workflows with unexpected encryption

---

## Common Issues and Troubleshooting

| Issue | Cause | Solution |
|---|---|---|
| Autopilot device stuck at "Identifying your device" | Hardware hash not uploaded or not yet processed | Verify the device appears in **Devices → Enrollment → Autopilot → Devices**; allow up to 15 minutes after import |
| ESP blocks enrollment indefinitely | A required app or policy failed to install | Check **Diagnostics** on the ESP screen; review Intune app install status and event logs under `Applications and Services Logs\Microsoft\Windows\DeviceManagement-Enterprise-Diagnostics-Provider` |
| Autopilot profile not applied after OOBE | Profile is not assigned to the group containing the device | Confirm group membership and dynamic rule evaluation; also confirm the device's Azure AD registration shows the ZTD tag |
| `Connect-MgGraph` fails with `AADSTS65001` | Admin consent not granted for the requested scopes | In Azure AD, navigate to **App registrations → [your app] → API permissions** and click **Grant admin consent** |
| Graph API returns `HTTP 403 Forbidden` | Service principal lacks the required application permission | Add and grant admin consent for the missing permission (e.g., `DeviceManagementManagedDevices.ReadWrite.All`) in the app registration |
| Graph API returns `HTTP 429 Too Many Requests` | API throttling limit reached | Implement a `Retry-After` delay; use `$batch` requests to reduce call volume; spread bulk operations over time |
| PowerShell script fails with `Insufficient privileges` | Delegated scopes not sufficient; missing role assignment | Ensure the signed-in user has the Intune Administrator or Global Administrator role in Azure AD |
| Sensitivity label not applied in managed app | Label not published to the user or app not updated | Verify the label policy targets the correct users; ensure the Microsoft 365 app version supports the label; force a policy refresh with `msedge://policy` (for Edge) |
| Bulk Autopilot CSV import partially fails | CSV contains malformed serial numbers or duplicate hashes | Validate the CSV with `Import-Csv` before import; remove duplicate rows; ensure no special characters in serial number fields |
| Automation runbook cannot authenticate to Graph | Managed Identity not granted the required Graph permissions | Assign Graph API application permissions to the system-assigned Managed Identity using `New-MgServicePrincipalAppRoleAssignment` |
| Logic App trigger not firing on Intune event | Event Hub namespace not connected or wrong consumer group | In the Logic App designer verify the Event Hub connection string and consumer group; check Event Hub metrics for incoming messages |
| Compliance policy assignment not reflected in portal | Assignment propagation delay | Allow up to 10 minutes for assignment changes to appear; refresh the browser and clear the portal cache |

---

## Assessment Questions

**Question 1:** What is the primary purpose of the Windows Autopilot Enrollment Status Page (ESP)?

<details>
<summary>Answer</summary>

The ESP provides a real-time progress screen during OOBE that displays the status of device configuration and app installations. Its primary purpose is to **block the user from accessing the device** until all required policies, certificates, and applications have been successfully applied, ensuring the device is fully compliant and configured before the user starts working.

</details>

---

**Question 2:** A PowerShell automation script that connects to Microsoft Graph using `Connect-MgGraph` returns `AADSTS65001: The user or administrator has not consented to use the application`. What is the most likely cause and how would you resolve it?

<details>
<summary>Answer</summary>

The error means that the Azure AD application registration has not been granted admin consent for the delegated or application permissions it is requesting. To resolve it:

1. Sign in to the **Azure portal** and navigate to **Azure Active Directory → App registrations → [your app] → API permissions**.
2. Verify that all required Microsoft Graph permissions are listed (e.g., `DeviceManagementManagedDevices.ReadWrite.All`).
3. Click **Grant admin consent for [tenant name]** and confirm.
4. For application (non-delegated) permissions, ensure a Global Administrator or Privileged Role Administrator has granted tenant-wide consent.

</details>

---

**Question 3:** You need to retrieve all managed devices from Intune using the Graph API, but the response only returns 100 devices even though there are 1,500 devices enrolled. What is happening and how do you retrieve all devices?

<details>
<summary>Answer</summary>

The Graph API paginates results and returns a maximum number of items per page (the default is often 100 for managed device queries). When more results exist, the response includes an `@odata.nextLink` property containing the URL for the next page.

To retrieve all devices, implement pagination:

```powershell
$uri     = "https://graph.microsoft.com/v1.0/deviceManagement/managedDevices"
$headers = @{ Authorization = "Bearer $accessToken" }
$allDevices = @()

do {
    $response   = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers
    $allDevices += $response.value
    $uri         = $response.'@odata.nextLink'
} while ($uri)

Write-Host "Total devices retrieved: $($allDevices.Count)"
```

Continue calling `@odata.nextLink` until it is null or absent, indicating all pages have been retrieved.

</details>

---

**Question 4:** Describe two meaningful differences between Windows Autopilot **User-Driven** mode and **Self-Deploying** mode. For what device types is each mode most appropriate?

<details>
<summary>Answer</summary>

| Aspect | User-Driven | Self-Deploying |
|---|---|---|
| User interaction | Requires the user to authenticate with Azure AD credentials during OOBE | Fully automated — no user credentials required |
| Device affinity | Device is associated with a specific user (Primary User) | Device has no user affinity (device-only enrollment) |
| TPM requirement | Standard TPM; device attestation not strictly required | **Requires TPM 2.0 with device attestation** support |
| Best for | Laptops and desktops assigned to individual employees | Kiosks, digital signage, shared workstations, meeting room devices |

**User-Driven** is ideal for corporate-owned personal devices where the user needs to authenticate and receive user-specific policies. **Self-Deploying** is ideal for shared or unmanned devices where there is no individual user and the device must configure itself completely autonomously.

</details>

---

**Question 5:** You want to configure an Azure Automation runbook to retire Intune devices that have not synced in 90 days. What authentication method should the runbook use, and what Graph API permission is required?

<details>
<summary>Answer</summary>

**Authentication method:** Use a **System-Assigned Managed Identity** on the Azure Automation account. This eliminates the need to manage credentials or rotate secrets. The Managed Identity's service principal must be granted the appropriate Microsoft Graph application permission.

**Required Graph API permission:** `DeviceManagementManagedDevices.PrivilegedOperations.All`

This permission is required to execute privileged actions such as retire, wipe, and reset passcode on managed devices.

**Additional steps to configure:**

1. Enable the Managed Identity on the Automation account in the Azure portal.
2. Use the following PowerShell to assign the Graph permission to the Managed Identity:
   ```powershell
   $miObjectId    = "<MANAGED_IDENTITY_OBJECT_ID>"
   $graphSpn      = Get-MgServicePrincipal -Filter "displayName eq 'Microsoft Graph'"
   $role          = $graphSpn.AppRoles | Where-Object { $_.Value -eq "DeviceManagementManagedDevices.PrivilegedOperations.All" }
   New-MgServicePrincipalAppRoleAssignment `
       -ServicePrincipalId $miObjectId `
       -PrincipalId        $miObjectId `
       -ResourceId         $graphSpn.Id `
       -AppRoleId          $role.Id
   ```
3. In the runbook, authenticate with `Connect-MgGraph -Identity` to use the Managed Identity token.

</details>

---

## Key Resources

- [Windows Autopilot overview — Microsoft Learn](https://learn.microsoft.com/en-us/autopilot/windows-autopilot)
- [Windows Autopilot deployment profiles — Microsoft Learn](https://learn.microsoft.com/en-us/autopilot/profiles)
- [Enrollment Status Page — Microsoft Learn](https://learn.microsoft.com/en-us/autopilot/enrollment-status)
- [Microsoft Purview Information Protection — Microsoft Learn](https://learn.microsoft.com/en-us/purview/information-protection)
- [Endpoint DLP — Microsoft Learn](https://learn.microsoft.com/en-us/purview/endpoint-dlp-learn-about)
- [App protection policies and sensitivity labels — Microsoft Learn](https://learn.microsoft.com/en-us/mem/intune/apps/app-protection-policy)
- [Microsoft Graph PowerShell SDK — Microsoft Learn](https://learn.microsoft.com/en-us/powershell/microsoftgraph/overview)
- [Get-WindowsAutoPilotInfo script — PowerShell Gallery](https://www.powershellgallery.com/packages/Get-WindowsAutoPilotInfo)
- [Microsoft Graph API reference — Intune](https://learn.microsoft.com/en-us/graph/api/resources/intune-graph-overview)
- [Graph Explorer](https://developer.microsoft.com/en-us/graph/graph-explorer)
- [Batch requests in Microsoft Graph — Microsoft Learn](https://learn.microsoft.com/en-us/graph/json-batching)
- [Azure Automation runbooks — Microsoft Learn](https://learn.microsoft.com/en-us/azure/automation/automation-runbook-types)
- [Azure Logic Apps — Microsoft Learn](https://learn.microsoft.com/en-us/azure/logic-apps/logic-apps-overview)
- [Power Automate — Microsoft Learn](https://learn.microsoft.com/en-us/power-automate/getting-started)
- [Microsoft Graph throttling guidance — Microsoft Learn](https://learn.microsoft.com/en-us/graph/throttling)
- [Assign Graph permissions to a Managed Identity — Microsoft Learn](https://learn.microsoft.com/en-us/azure/app-service/scenario-secure-app-access-microsoft-graph-as-app)

---

## Next Steps

You have completed **Module 09: Integrations & Advanced Automation**. You are now equipped to:

- Provision devices at scale with Windows Autopilot and zero-touch OOBE
- Layer Microsoft Purview DLP and sensitivity labels on top of Intune MAM to create a unified data governance posture
- Automate Intune management tasks with PowerShell and the Microsoft Graph SDK
- Build and troubleshoot direct Graph API integrations for custom tooling
- Design cloud-native automation pipelines using Azure Automation, Logic Apps, and Power Automate

**Proceed to [Module 10: Best Practices & Governance](../Module-10-Best-Practices/README.md)** to learn how to operationalise everything covered in this course — including role-based access control, change management, update ring strategies, and long-term Intune governance frameworks.

If you want to reinforce the skills from this module before moving on, revisit **[Module 06: Security & Compliance](../Module-06-Security-Compliance/README.md)** to review how compliance policies interact with the Conditional Access and DLP integrations introduced here.


# Module 08: Reporting & Analytics

## Module Overview

Visibility into your endpoint environment is essential for maintaining security, compliance, and operational health. Microsoft Intune provides a rich set of built-in reports, Endpoint Analytics insights, and integrations with Azure Monitor Log Analytics and Power BI that give administrators a comprehensive picture of device status, app health, and user productivity. In this module, you will explore every major reporting surface available in Intune, learn how to query raw telemetry with Kusto Query Language (KQL), and build custom dashboards that surface actionable insights for your organization.

---

## Learning Objectives

By the end of this module, you will be able to:

- Navigate and interpret the built-in Intune reports for device compliance, configuration, and app status
- Enable and configure Endpoint Analytics to measure startup performance, app reliability, and the Work from Anywhere score
- Connect Intune diagnostic data to an Azure Monitor Log Analytics workspace
- Write KQL queries to correlate Intune data with other Azure and Microsoft 365 telemetry
- Integrate Intune reporting data with Power BI for executive-level dashboards
- Monitor application installation status and investigate deployment failures
- Build custom reports using the Microsoft Graph API OData export endpoint
- Establish a proactive compliance monitoring and remediation workflow

---

## Key Topics

### 1. Intune Built-In Reports

Microsoft Intune includes a dedicated **Reports** node in the Microsoft Intune admin center ([intune.microsoft.com](https://intune.microsoft.com)). Reports are organized by workload:

| Report Category | Examples |
|---|---|
| **Device compliance** | Compliance by policy, noncompliant devices, compliance trend |
| **Device configuration** | Assignment failures, settings conflicts |
| **Endpoint security** | Antivirus status, firewall status, encryption status (BitLocker) |
| **Apps** | App install status, app protection policy status, discovered apps |
| **Updates** | Windows Update for Business compliance, feature update status |
| **Enrollment** | Enrollment failures, autopilot deployments |

Key capabilities:

- **Filter and sort** report data by OS platform, policy, user, device, or compliance state
- **Export to CSV** for offline analysis or integration into other tools
- **Trend views** show compliance changes over a configurable time window (7, 14, or 30 days)
- Reports are near-real-time; most refresh within 24 hours, some within minutes

---

### 2. Endpoint Analytics

**Endpoint Analytics** is a cloud-native analytics service that measures end-user experience metrics and provides recommendations to improve productivity and reduce support costs. It is accessible from the **Reports > Endpoint Analytics** blade.

#### Key Scores and Metrics

| Metric | Description |
|---|---|
| **Endpoint Analytics score** | Composite score (0-100) representing overall endpoint health |
| **Startup performance score** | Time-to-productive based on boot and sign-in duration |
| **App reliability score** | Mean time between app crashes weighted by usage frequency |
| **Work from Anywhere score** | Readiness for cloud-managed, location-independent work |
| **Battery health** | Estimated battery capacity vs. design capacity across the fleet |

#### Startup Performance Drill-Down

- **Boot score**: Core boot, Group Policy processing, user profile load, desktop ready
- **Sign-in score**: Credential provider, background processes, shell ready
- Identifies top processes delaying startup so you can target remediation scripts

#### Proactive Remediations (Remediations)

Endpoint Analytics includes a **Remediations** feature (formerly Proactive Remediations) that runs script pairs (detect + remediate) on a schedule. Common use cases:

- Detect and clear stale Windows Update cache
- Detect and re-enroll devices with expired certificates
- Detect and fix misconfigured DNS suffixes

---

### 3. Log Analytics Workspace Integration

Intune can stream diagnostic data to an **Azure Monitor Log Analytics workspace** via **Diagnostic settings**. This unlocks:

- Long-term retention beyond Intune's 30-day built-in window
- Correlation with Azure AD, Defender for Endpoint, and other data sources
- Advanced KQL queries and workbooks
- Alerting via Azure Monitor alert rules

#### Supported Data Categories

| Category | Log Table |
|---|---|
| Audit logs | `IntuneAuditLogs` |
| Operational logs | `IntuneOperationalLogs` |
| Device compliance org | `IntuneDeviceComplianceOrg` |
| Device configuration | `IntuneDevices` |
| Enrollment failures | `IntuneDeviceEnrollmentFailures` |

#### Prerequisites

- An Azure subscription with a Log Analytics workspace
- Intune Administrator role (or Global Administrator)
- Diagnostic settings configured under **Tenant administration > Diagnostic settings**

---

### 4. Microsoft Monitoring Agent / Diagnostic Data

Intune collects device-side diagnostic data through several channels:

- **Windows Diagnostic Data** (telemetry): Required for Endpoint Analytics; minimum level is **Required** (formerly Basic). Set via Device Configuration profile > Reporting and Telemetry.
- **Intune Management Extension (IME) logs**: Collected automatically for Win32 app deployments and PowerShell scripts. Viewable in the admin center under the device's **Managed apps** blade.
- **Device diagnostics collection**: Administrators can trigger on-demand log collection from a device's **Device diagnostics** action. Logs are uploaded to the admin center and retained for 28 days.
- **Windows health monitoring**: A Device Configuration profile type that enables collection of Windows event log channels into Endpoint Analytics.

---

### 5. Power BI Integration

Intune data can be visualized in **Power BI** using two primary methods:

#### Method A — Intune Data Warehouse (OData)

- Navigate to **Reports > Intune Data Warehouse** to obtain the OData feed URL
- Connect Power BI Desktop using **Get Data > OData Feed**
- Authenticate with your Entra ID (Azure AD) credentials
- Tables include `devices`, `users`, `devicePropertyHistories`, `mobileApps`, and more
- Schedule automatic refresh via Power BI Service with a gateway or service principal

#### Method B — Log Analytics Connector

- Use the **Azure Monitor (Log Analytics)** connector in Power BI Desktop
- Query `IntuneDeviceComplianceOrg`, `IntuneAuditLogs`, etc. directly with KQL
- Combine with other Azure data sources in the same Power BI report

#### Report Design Tips

- Use slicers for **OS platform**, **compliance state**, and **enrollment date**
- Add KPI cards for noncompliant device count and compliance percentage
- Schedule daily refresh to keep dashboards current

---

### 6. Compliance Monitoring and Remediation

A proactive compliance posture requires continuous monitoring, not just point-in-time reports.

#### Recommended Workflow

1. **Define compliance policies** with clear marking periods (grace periods of 1-3 days for minor issues)
2. **Create device groups** based on compliance state using dynamic Azure AD groups (`(device.deviceComplianceStatus -eq "Noncompliant")`)
3. **Configure conditional access** to block or limit noncompliant devices from accessing corporate resources
4. **Set up email notifications** (Actions for noncompliance) to alert end users and IT helpdesk
5. **Review the Compliance trend report** weekly; set a compliance target (e.g., >= 95%)
6. **Use Endpoint Analytics Remediations** to auto-remediate known issues

#### Noncompliance Actions

| Action | Timing | Use Case |
|---|---|---|
| Send email to user | Immediately | Inform user their device is noncompliant |
| Mark device noncompliant | After grace period | Trigger conditional access |
| Remotely lock device | After N days | High-security scenarios |
| Retire device | After extended period | Abandoned or lost devices |

---

### 7. Custom Reports Using OData / Graph API Exports

For organizations with advanced reporting needs, the **Microsoft Graph API** provides programmatic access to Intune data.

#### OData Export Endpoint

```
GET https://graph.microsoft.com/v1.0/deviceManagement/reports/exportJobs
```

Supported report names include `DeviceCompliance`, `DeviceInstallStatusByApp`, `UserInstallStatusAggregateByApp`, and more.

#### Example: Trigger a Compliance Export

```http
POST https://graph.microsoft.com/v1.0/deviceManagement/reports/exportJobs
Content-Type: application/json

{
  "reportName": "DeviceCompliance",
  "filter": "",
  "select": ["DeviceId","DeviceName","ComplianceState","OS","LastContact"],
  "format": "csv"
}
```

Poll the returned `id` until `status` is `completed`, then download the CSV from the `url` property.

#### PowerShell Automation

Use the **Microsoft.Graph** PowerShell module to automate report generation and delivery:

```powershell
Connect-MgGraph -Scopes "DeviceManagementManagedDevices.Read.All"
$job = New-MgDeviceManagementReportExportJob -ReportName "DeviceCompliance" -Format "csv"
# Poll until complete, then Invoke-WebRequest $job.Url -OutFile "compliance.csv"
```

---

## Hands-On Labs

### Lab 8.1: Review Device Compliance Reports

**Objective:** Navigate the built-in compliance reports, apply filters, interpret the data, and export results for offline review.

**Prerequisites:**
- Microsoft Intune admin center access
- At least one compliance policy assigned to devices
- Devices enrolled and reporting compliance state

**Steps:**

1. Sign in to the **Microsoft Intune admin center** at [https://intune.microsoft.com](https://intune.microsoft.com) using an account with the **Intune Administrator** or **Read Only Operator** role.

2. In the left navigation pane, select **Reports**. On the Reports Overview page, review the summary tiles showing compliant vs. noncompliant device counts.

3. Select **Device compliance > Reports > Device compliance**. Wait for the report to load. Note the columns: Device name, User, OS, Compliance state, Last check-in.

4. Click **Add filter** and add the following filters:
   - **OS**: Windows 10 and later
   - **Compliance**: Noncompliant

   Click **Apply**. Observe that the list now shows only noncompliant Windows devices.

5. Click on any device name in the list to open its **Device compliance detail** panel. Review which specific compliance settings are failing (e.g., BitLocker not enabled, OS version below minimum).

6. Return to the report list. Click **Add filter > Last check-in** and set the range to the last **7 days**. This identifies devices that have recently checked in.

7. Click the **Columns** button and add **Device ID**, **Serial number**, and **Ownership** (Corporate vs. Personal) to the view.

8. Click **Export** (top-right of the report) and save the CSV file to your workstation. Open it in Excel and create a pivot table summarizing noncompliant devices by OS version.

9. Navigate to **Reports > Device compliance > Compliance trend**. Set the time window to **30 days**. Note any spikes or drops in compliance percentage and correlate them to recent policy changes.

10. Navigate to **Reports > Endpoint security > Antivirus agent status**. Confirm that all enrolled devices show **Reporting** status for Microsoft Defender Antivirus.

**Expected Outcome:** You can confidently navigate compliance reports, isolate noncompliant devices by platform, drill into per-device failure reasons, and export data for stakeholder reporting.

---

### Lab 8.2: Enable Endpoint Analytics

**Objective:** Onboard devices to Endpoint Analytics, enable data collection, connect to a Log Analytics workspace, and interpret the initial scores.

**Prerequisites:**
- Microsoft Intune admin center access with Intune Administrator role
- An Azure subscription with Owner or Contributor access
- Windows 10 (1903+) or Windows 11 devices enrolled in Intune
- Devices must send Windows Required (Basic) telemetry or higher

**Steps:**

1. In the **Microsoft Intune admin center**, navigate to **Reports > Endpoint Analytics**.

2. If this is the first time enabling Endpoint Analytics, click **Start**. Read the data-sharing notice and click **Start** again to confirm.

3. Navigate to **Endpoint Analytics > Settings**. Under **Intune data collection policy**, confirm that the toggle is set to **Enabled**. Click **Save** if you make any changes.

4. To ensure devices send the required telemetry, navigate to **Devices > Configuration > Create > New policy**. Select:
   - Platform: **Windows 10 and later**
   - Profile type: **Settings catalog**
   
   Search for **Allow Telemetry** and set it to **Required (1)**. Assign the profile to your pilot device group and click **Create**.

5. Return to **Reports > Endpoint Analytics**. The **Endpoint Analytics score** card will initially show **No data** for new tenants. Scores typically populate within 24-48 hours after devices check in.

6. Once data is available, click **Startup performance**. Review the list of devices sorted by startup duration. Identify the top 5 slowest-booting devices.

7. Click on one of the slow-booting devices. Review the **Boot process** timeline showing each phase: Pre-boot, Boot, Group Policy, Desktop. Note which phase is consuming the most time.

8. Navigate to **App reliability** under Endpoint Analytics. Review the **App reliability score** and identify any apps with frequent crashes or hangs. Click on an app name to see affected devices.

9. Navigate to **Work from Anywhere**. Review each category score: Cloud management, Cloud identity, Cloud provisioning. Note any categories scoring below 50 and review the associated recommendations.

10. Under **Remediations**, review any available Microsoft-provided script packages. Click **+ Create script package** to explore creating a custom detect/remediate script pair for your environment.

**Expected Outcome:** Endpoint Analytics is enabled, devices are enrolled and sending telemetry, and you can read startup performance scores, app reliability data, and Work from Anywhere recommendations.

---

### Lab 8.3: Create Custom Report with Log Analytics

**Objective:** Configure Intune Diagnostic settings to stream data to a Log Analytics workspace, then write KQL queries to build a custom compliance dashboard.

**Prerequisites:**
- Azure subscription with a Log Analytics workspace (or permission to create one)
- Intune Administrator role and Azure Contributor role
- At least 24 hours of data streaming (if configuring for the first time)

**Steps:**

1. In the **Azure portal** ([portal.azure.com](https://portal.azure.com)), navigate to **Log Analytics workspaces**. Create a new workspace (or select an existing one):
   - Subscription: Your Azure subscription
   - Resource group: `rg-intune-monitoring`
   - Name: `law-intune-prod`
   - Region: Select your preferred region
   
   Click **Review + Create**, then **Create**.

2. In the **Microsoft Intune admin center**, navigate to **Tenant administration > Diagnostic settings**. Click **+ Add diagnostic setting**.

3. In the Diagnostic setting blade, provide a name (e.g., `Intune-to-LogAnalytics`). Check the following log categories:
   - **AuditLogs**
   - **OperationalLogs**
   - **DeviceComplianceOrg**
   - **Devices**

4. Under **Destination details**, check **Send to Log Analytics workspace**. Select your subscription and the `law-intune-prod` workspace. Click **Save**.

5. Wait at least 15-30 minutes for initial data to flow. In the **Azure portal**, navigate to your Log Analytics workspace and select **Logs**.

6. In the KQL query editor, run the following query to view the most recently noncompliant devices:

   ```kql
   IntuneDeviceComplianceOrg
   | where TimeGenerated > ago(7d)
   | where ComplianceState == "noncompliant"
   | project TimeGenerated, DeviceName, UserName, OS, OSVersion, ComplianceState, LastContact
   | order by LastContact desc
   | take 50
   ```

7. Extend the query to summarize noncompliance by OS version and visualize as a bar chart:

   ```kql
   IntuneDeviceComplianceOrg
   | where TimeGenerated > ago(7d)
   | where ComplianceState == "noncompliant"
   | summarize NoncompliantCount = count() by OS, OSVersion
   | order by NoncompliantCount desc
   | render barchart
   ```

8. Run a query to identify devices that have not checked in for more than 14 days:

   ```kql
   IntuneDeviceComplianceOrg
   | where TimeGenerated > ago(1d)
   | extend LastContactDate = todatetime(LastContact)
   | where LastContactDate < ago(14d)
   | project DeviceName, UserName, OS, LastContact, ComplianceState
   | order by LastContactDate asc
   ```

9. Click **Save > Save as query**. Name the query `Noncompliant Devices - Last 7 Days` and save it to the **Shared queries** section so other team members can use it.

10. Click **Pin to dashboard** on one of your query result charts. Create a new Azure dashboard named `Intune Compliance Dashboard`. Add all three query results as tiles.

**Expected Outcome:** Intune diagnostic data flows into Log Analytics, you can write KQL queries to analyze compliance and check-in data, and you have a pinned Azure dashboard with live compliance charts.

---

### Lab 8.4: Monitor Application Installation Status

**Objective:** Review per-device and per-app installation status reports to identify deployment failures and investigate root causes using IME logs.

**Prerequisites:**
- At least one Win32 app or Microsoft Store app deployed via Intune
- Devices enrolled with the Intune Management Extension (IME) installed
- Intune Administrator or Help Desk Operator role

**Steps:**

1. In the **Microsoft Intune admin center**, navigate to **Apps > All apps**. Search for and select the app you want to investigate (e.g., `7-Zip`, `Microsoft Teams`, or a custom Line of Business app).

2. In the app's **Overview** blade, review the **Device install status** and **User install status** summary tiles. Note the counts for Installed, Failed, Pending, and Not Applicable.

3. Select **Device install status** from the Monitor section. The report shows every assigned device and its install state. Apply a filter:
   - **Install status**: Failed
   
   This isolates all devices where the app failed to install.

4. Click on a failed device to open its device detail page. Under **Managed apps**, locate the app entry. Review the **Installation details** field which shows the MSI exit code or Win32 app detection rule result.

5. On the same device page, click **Collect diagnostics** (under the **...** More actions menu). Confirm the collection request. This triggers log upload from the device.

6. After a few minutes, refresh the device page. Under **Device diagnostics**, download the collected diagnostics ZIP file. Extract it and navigate to `\MDMDiagReport\` to find the `IntuneManagementExtension.log`.

7. Open `IntuneManagementExtension.log` in a text editor or CMTrace log viewer. Search for the failing app's **app ID** (visible in the Intune admin center URL when viewing the app). Look for error lines such as:
   - `Installation failed with exit code 1603` (MSI general failure)
   - `Detection failed` (app installed but detection rule returned false)
   - `Download failed` (connectivity or storage issue)

8. Return to the admin center. Navigate to **Apps > All apps > [App name] > User install status**. Review whether failures are associated with specific users or groups, which may indicate a permission or profile issue.

9. Navigate to **Reports > Apps > App install status report**. Set the filter to your app. Export the CSV report and confirm it matches what you saw in the per-app view.

10. If you identified a fix (e.g., corrected detection rule, updated installer), navigate to **Apps > [App name] > Properties** and update the relevant setting. Click **Save**. Then go to **Device install status**, select a failed device, and click **Retry** to trigger a fresh installation attempt.

**Expected Outcome:** You can locate app deployment failures at the per-device level, collect and interpret IME diagnostic logs to determine root cause, and initiate remediation steps for failed installations.

---

## Best Practices

### Do's

- ✅ **Enable Endpoint Analytics** from the start of your Intune deployment to establish a performance baseline before any remediation work
- ✅ **Stream diagnostic data** to Log Analytics for all production tenants to enable long-term trend analysis and auditing
- ✅ **Export compliance reports** on a scheduled basis (weekly or monthly) and store them for audit and regulatory purposes
- ✅ **Use dynamic Azure AD groups** based on compliance state to automatically scope conditional access and remediation policies
- ✅ **Set a compliance grace period** (1-3 days) to allow devices time to self-remediate before being blocked
- ✅ **Review the Work from Anywhere score** quarterly and use its recommendations to improve cloud management readiness
- ✅ **Monitor IME logs** for Win32 app deployments in staging before wide rollout to catch installation failures early
- ✅ **Use named KQL queries** in Log Analytics and share them with your team to standardize reporting

### Don'ts

- ❌ **Do not rely solely on the 30-day Intune report window** for compliance history; use Log Analytics for retention beyond 30 days
- ❌ **Do not ignore the Enrollment failure report**; unresolved enrollment failures silently reduce your managed device coverage
- ❌ **Do not grant broad Global Administrator access** just for reporting; use the built-in **Intune Read Only Operator** role for read-only access
- ❌ **Do not overlook the App reliability score**; poor app reliability is a leading indicator of user productivity loss and help desk ticket volume
- ❌ **Do not publish Power BI reports** containing device or user PII to external audiences without appropriate data governance controls
- ❌ **Do not skip the detection rule validation** for Win32 apps; a misconfigured detection rule causes Intune to report failure even when the app is installed correctly

---

## Common Issues and Troubleshooting

| Issue | Cause | Solution |
|---|---|---|
| Endpoint Analytics shows "No data" after 48 hours | Devices not sending Required telemetry; IME not installed | Verify the **Allow Telemetry** CSP setting is set to Required (1) or higher via a configuration profile; confirm IME is installed for Windows devices |
| Log Analytics tables are empty after enabling Diagnostic settings | Propagation delay or misconfigured Diagnostic settings | Allow up to 1 hour for initial data; verify the correct workspace is selected and the log categories are checked in Diagnostic settings |
| Compliance report shows devices as "Not evaluated" | Compliance policy not assigned to the device or device group | Review policy assignments and confirm the device is a member of the targeted group; trigger a device sync |
| App install shows "Failed" with exit code 1603 | Windows Installer internal error; often a conflicting installation or missing prerequisite | Check if a previous version of the app is already installed; review system event logs on the device; ensure SYSTEM account has write access to the install directory |
| Power BI OData refresh fails with 401 Unauthorized | The authenticating account's token has expired or lacks permissions | Re-enter credentials in Power BI Desktop; for scheduled refresh, configure a service principal with `DeviceManagementManagedDevices.Read.All` permission |
| KQL query returns no results for IntuneDeviceComplianceOrg | Table may not have been populated yet or the time filter is too narrow | Check TimeGenerated range; run `IntuneDeviceComplianceOrg | take 10` to confirm the table has data |
| Device diagnostics collection times out | Device is offline or the Intune Management Extension is not running | Confirm device is online and connected; check IME service status (`sc query IntuneManagementExtension`) on the device |
| Compliance trend shows sudden drop | New compliance policy with stricter settings was assigned | Review recent policy changes in the **Audit logs** report; if intentional, communicate to stakeholders; if accidental, roll back the policy change |

---

## Assessment Questions

**1. Which minimum Windows telemetry level is required for Endpoint Analytics to collect startup performance data?**

<details>
<summary>Answer</summary>

**Required (formerly Basic) — level 1.** Endpoint Analytics requires at least the **Required** diagnostic data level. This is configured via a Device Configuration profile using the **Allow Telemetry** CSP setting with a value of `1`. Higher levels (Enhanced, Full) provide additional data but Required is the minimum.

</details>

---

**2. A security auditor requests 90 days of Intune audit log history. The Intune admin center only retains 30 days. How do you fulfil this request?**

<details>
<summary>Answer</summary>

Configure **Diagnostic settings** under **Tenant administration > Diagnostic settings** in the Intune admin center to stream **AuditLogs** to an **Azure Monitor Log Analytics workspace**. Log Analytics can retain data for up to 2 years (configurable). Query `IntuneAuditLogs` with KQL to retrieve the required 90-day window and export the results for the auditor.

</details>

---

**3. A Win32 app shows status "Failed" on 15 devices with exit code 1603. What are the first two troubleshooting steps?**

<details>
<summary>Answer</summary>

1. **Collect device diagnostics** for one of the affected devices using the **Collect diagnostics** device action in the Intune admin center. Download the ZIP, extract it, and open the `IntuneManagementExtension.log` file. Search for the app's ID to find the specific error context around the 1603 exit code.

2. **Check for a conflicting installation**: Exit code 1603 is a generic Windows Installer error often caused by an existing version of the app already installed, a locked file, or insufficient permissions. Connect to one of the failing devices and check **Programs and Features** (or **Apps & Features**) to see if the app is already present. Also review the Windows Application event log (`eventvwr.msc`) for MsiInstaller entries at the time of the failure.

</details>

---

**4. What KQL query would you write to find all devices in the IntuneDeviceComplianceOrg table that have not checked in for more than 30 days, and are currently marked as noncompliant?**

<details>
<summary>Answer</summary>

```kql
IntuneDeviceComplianceOrg
| where TimeGenerated > ago(1d)
| extend LastContactDate = todatetime(LastContact)
| where LastContactDate < ago(30d)
| where ComplianceState == "noncompliant"
| project DeviceName, UserName, OS, OSVersion, LastContact, ComplianceState
| order by LastContactDate asc
```

This query reads the most recent snapshot (`ago(1d)`), converts `LastContact` to a datetime, filters for devices not seen in 30 days, and further restricts to noncompliant state. The results are ordered by oldest check-in first to prioritize the most stale devices.

</details>

---

**5. Your organization wants to give the security operations team read-only access to Intune compliance reports without allowing them to make any configuration changes. What is the least-privileged built-in role assignment?**

<details>
<summary>Answer</summary>

Assign the built-in **Intune Read Only Operator** role. This role grants read access to all Intune data including reports, device details, compliance status, and app status, but does not permit any create, update, or delete operations. It is the least-privileged built-in role that satisfies read-only reporting access. Assign it scoped to **All devices** and **All users** scope groups as appropriate for the security team's responsibilities.

</details>

---

## Key Resources

| Resource | Link |
|---|---|
| Intune reports overview | [https://learn.microsoft.com/en-us/mem/intune/fundamentals/reports](https://learn.microsoft.com/en-us/mem/intune/fundamentals/reports) |
| Endpoint Analytics overview | [https://learn.microsoft.com/en-us/mem/analytics/overview](https://learn.microsoft.com/en-us/mem/analytics/overview) |
| Startup performance in Endpoint Analytics | [https://learn.microsoft.com/en-us/mem/analytics/startup-performance](https://learn.microsoft.com/en-us/mem/analytics/startup-performance) |
| Work from Anywhere report | [https://learn.microsoft.com/en-us/mem/analytics/work-from-anywhere](https://learn.microsoft.com/en-us/mem/analytics/work-from-anywhere) |
| Send log data to Log Analytics | [https://learn.microsoft.com/en-us/mem/intune/fundamentals/review-logs-using-azure-monitor](https://learn.microsoft.com/en-us/mem/intune/fundamentals/review-logs-using-azure-monitor) |
| KQL reference for Log Analytics | [https://learn.microsoft.com/en-us/azure/data-explorer/kql-quick-reference](https://learn.microsoft.com/en-us/azure/data-explorer/kql-quick-reference) |
| Intune Data Warehouse API | [https://learn.microsoft.com/en-us/mem/intune/developer/reports-nav-create-intune-reports](https://learn.microsoft.com/en-us/mem/intune/developer/reports-nav-create-intune-reports) |
| Graph API export jobs | [https://learn.microsoft.com/en-us/graph/api/intune-reporting-devicemanagementreports-exportjob-create](https://learn.microsoft.com/en-us/graph/api/intune-reporting-devicemanagementreports-exportjob-create) |
| Power BI integration with Intune | [https://learn.microsoft.com/en-us/mem/intune/developer/reports-nav-create-intune-reports#power-bi](https://learn.microsoft.com/en-us/mem/intune/developer/reports-nav-create-intune-reports#power-bi) |
| Remediations (Proactive Remediations) | [https://learn.microsoft.com/en-us/mem/analytics/remediations](https://learn.microsoft.com/en-us/mem/analytics/remediations) |
| Win32 app troubleshooting | [https://learn.microsoft.com/en-us/mem/intune/apps/troubleshoot-app-install](https://learn.microsoft.com/en-us/mem/intune/apps/troubleshoot-app-install) |

---

## Next Steps

After completing this module, you are ready to move on to:

- **Module 09: Role-Based Access Control (RBAC) and Scope Tags** — Learn how to delegate Intune administration to multiple IT teams using built-in and custom roles, and use scope tags to enforce administrative boundaries across device groups.

**Reinforce what you learned in this module by:**

- Setting up a recurring weekly email export of the Device Compliance report using Power Automate and the Graph API
- Creating an Azure Monitor alert rule that fires when noncompliant device count exceeds a threshold (e.g., 50 devices)
- Writing a Remediations script pair that detects and re-enables the Windows Update service if it has been disabled
- Building a Power BI report that combines Intune compliance data with Microsoft Defender for Endpoint risk scores to produce a unified endpoint risk view

---

*Module 08 of the Microsoft Intune Endpoint Administration Course*

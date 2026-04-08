# Module 08: Reporting & Analytics

[![Module Status](https://img.shields.io/badge/Status-Active-success)](https://github.com)
[![Labs](https://img.shields.io/badge/Labs-4-blue)](README.md)
[![Duration](https://img.shields.io/badge/Duration-4--5%20hours-orange)](README.md)

---

## 📋 Module Overview

Microsoft Intune provides rich reporting and analytics capabilities to help administrators understand the state of their device fleet, track compliance, monitor application deployments, and identify issues before they impact users. This module covers built-in Intune reports, Endpoint Analytics, Log Analytics integration, and custom reporting using KQL queries and Power BI.

---

## 🎯 Learning Objectives

By the end of this module, you will be able to:

- **Navigate** the Intune built-in reports for devices, apps, and compliance
- **Enable and interpret** Endpoint Analytics data (startup performance, app reliability)
- **Connect** Intune to a Log Analytics workspace for advanced querying
- **Write** basic KQL queries to extract compliance and device insights
- **Monitor** application installation status per device and per app
- **Export** reports and integrate with Power BI for dashboards
- **Configure** diagnostic settings for audit and operational logs
- **Identify** non-compliant devices and track remediation progress

---

## 📚 Key Topics

### 1. Intune Built-In Reports

Intune includes a dedicated **Reports** section with organised categories:

#### Report Categories
| Category | Examples |
|----------|---------|
| **Device Management** | Enrolled devices, device inventory, managed device details |
| **Endpoint Analytics** | Startup score, app reliability, work from anywhere |
| **App Management** | App install status, app protection status, MAM reports |
| **Device Compliance** | Compliance status, non-compliant devices, per-policy status |
| **Security** | Encryption report, Windows health attestation |
| **Audit Logs** | Admin activity, change history |

#### Accessing Reports
- **Intune Admin Center** > **Reports** (dedicated reports hub)
- **Devices** > individual device > view per-device reports
- **Apps** > individual app > installation status

#### Exporting Reports
- All reports support **CSV export**
- Graph API supports programmatic report export: `GET /deviceManagement/reports`

---

### 2. Endpoint Analytics

Endpoint Analytics is part of Microsoft Productivity Score and provides device experience insights.

#### Key Scores
| Score | What It Measures |
|-------|-----------------|
| **Startup performance** | Boot time, sign-in time, time to responsive desktop |
| **App reliability** | App crash and hang rates |
| **Work from anywhere** | Cloud management, cloud identity, cloud provisioning readiness |
| **Battery health** | Estimated battery capacity vs design capacity |
| **Resource performance** | CPU/RAM spike frequency |

#### Requirements
- Devices must be enrolled in Intune (MDM managed)
- Windows 10 1903+ or Windows 11
- Intune connector to Log Analytics (optional, for custom queries)

#### Baseline Comparison
Endpoint Analytics lets you compare your organisation scores against:
- **All organisations** median
- **Similar organisations** (by size/industry)

---

### 3. Log Analytics Integration

Log Analytics is an Azure Monitor workspace that stores Intune diagnostic data for advanced analysis.

#### Data Sent to Log Analytics
- Compliance policy results (`IntuneDeviceComplianceOrg`)
- Device configuration profiles (`IntuneDeviceConfigurationAndPolicy`)
- Audit logs (`AuditLogs`)
- Operational logs (`IntuneOperationalLogs`)
- Enrollment logs

#### KQL Query Examples

**Non-compliant devices:**
```kql
IntuneDeviceComplianceOrg
| where ComplianceState == "noncompliant"
| project DeviceName, UserName, OS, ComplianceState, LastContact
| order by LastContact desc
```

**Device compliance trend over 7 days:**
```kql
IntuneDeviceComplianceOrg
| summarize CompliantCount = countif(ComplianceState == "compliant"),
            NonCompliantCount = countif(ComplianceState == "noncompliant")
            by bin(TimeGenerated, 1d)
| render timechart
```

**App installation failures:**
```kql
IntuneOperationalLogs
| where OperationName == "ApplicationInstallation"
| where Result == "Failure"
| project TimeGenerated, DeviceName, ApplicationName, ErrorCode
| order by TimeGenerated desc
```

---

### 4. Compliance Monitoring and Remediation

#### Compliance Dashboard
The Intune compliance dashboard shows:
- Total devices and compliance breakdown (pie chart)
- Trends over time
- Per-policy compliance breakdown
- Per-setting compliance status

#### Compliance Actions
For non-compliant devices, you can automate:
- Email notification to user
- Push notification via Company Portal
- Remote lock after grace period
- Device retirement after extended non-compliance

#### Proactive Remediations (Endpoint Analytics)
Proactive Remediations run detection and remediation scripts on schedule:
- **Detection script**: checks for an issue (exits 1 if issue found)
- **Remediation script**: fixes the issue
- Results are viewable per device in the Endpoint Analytics portal

---

### 5. Power BI Integration

For custom dashboards, connect Power BI to Intune via:
1. **Intune Data Warehouse** (OData feed)
2. **Log Analytics** (Power BI connector)
3. **Graph API** (custom connector)

#### Intune Data Warehouse OData Feed
```
https://fef.msua06.manage.microsoft.com/ReportingService/DataWarehouseFEService?api-version=v1.0
```
Use this as the OData source in Power BI Desktop.

---

## 🧪 Hands-On Labs

---

### Lab 8.1: Review Device Compliance Reports

**Objective**: Navigate Intune built-in reports to identify non-compliant devices and compliance trends

**Steps**:

1. Sign in to [https://intune.microsoft.com](https://intune.microsoft.com)
2. Navigate to **Reports** in the left navigation
3. Under **Device compliance**, click **Compliance report (Organisational)**
4. Review the compliance status pie chart — note the percentage of compliant vs non-compliant devices
5. Click **Generate report** to get a detailed device list
6. Filter by **Compliance status = Not compliant** to see only failing devices
7. Click on a non-compliant device to view which specific settings are failing
8. Navigate to **Reports** > **Device compliance** > **Setting compliance**
9. Review which individual compliance settings are most frequently failing across your device fleet
10. Click **Export** to download the report as a CSV file
11. Navigate to **Devices** > **Monitor** > **Noncompliant devices** for a quick view
12. Document any patterns you observe (e.g., all failures on BitLocker requirement)

**Expected Outcome**: You can identify specific non-compliant devices, which settings are failing, and export data for further analysis

---

### Lab 8.2: Enable Endpoint Analytics

**Objective**: Enable Endpoint Analytics and review device startup performance scores

**Steps**:

1. In the Intune admin center, navigate to **Reports** > **Endpoint Analytics**
2. Click **Start** on the Endpoint Analytics overview page (first-time setup)
3. Review the **Baseline scores** — note your organisation score vs the median
4. Click **Startup performance** in the left menu
5. Review the **Startup score** chart and the **Top startup processes** table
6. Identify devices with the longest boot times by sorting the device list by **Time to responsive desktop**
7. Click on a specific device to see its detailed startup timeline (GP processing, service startup, etc.)
8. Navigate to **App reliability** and review the **App reliability score**
9. View the **Top apps impacting reliability** — these are apps with high crash/hang rates
10. Navigate to **Work from anywhere** and review cloud management, identity, and provisioning scores
11. Click **Settings** > **Baseline** and compare against All organisations
12. Note which categories are below the median and note them for improvement planning

**Expected Outcome**: You have an overview of device experience health and can identify specific devices and apps that are degrading performance scores

---

### Lab 8.3: Create a Custom Report with Log Analytics

**Objective**: Connect Intune to a Log Analytics workspace and run KQL queries on compliance data

**Steps**:

1. In the Azure Portal ([https://portal.azure.com](https://portal.azure.com)), create a Log Analytics workspace:
   - Navigate to **Log Analytics workspaces** > **+ Create**
   - Resource group: use your Intune lab resource group
   - Name: `intune-lab-logs`
   - Region: choose the same region as your tenant
   - Click **Review + create** > **Create**
2. Back in the Intune admin center, navigate to **Reports** > **Diagnostic settings**
3. Click **+ Add diagnostic setting**
4. Name it `Intune-to-LogAnalytics`
5. Check these log categories:
   - ✅ AuditLogs
   - ✅ OperationalLogs
   - ✅ DeviceComplianceOrg
   - ✅ DeviceConfigurationAndPolicy
6. Under **Destination details**, select **Send to Log Analytics workspace** and select `intune-lab-logs`
7. Click **Save**
8. Wait 15–30 minutes for data to begin flowing
9. Return to the Log Analytics workspace > **Logs**
10. Run this query to see compliance data:
```kql
IntuneDeviceComplianceOrg
| where TimeGenerated > ago(1d)
| summarize count() by ComplianceState
| render piechart
```
11. Run this query to find non-compliant devices:
```kql
IntuneDeviceComplianceOrg
| where ComplianceState == "noncompliant"
| project DeviceName, UserName, OS, ComplianceState, PolicyName
| order by DeviceName asc
```
12. Click **Save** > **Save as query** to save for future use

**Expected Outcome**: Intune compliance data flows into Log Analytics and you can query it with KQL to produce custom compliance insights

---

### Lab 8.4: Monitor Application Installation Status

**Objective**: Review per-device and per-app installation status, identify failures, and investigate error codes

**Steps**:

1. In Intune, navigate to **Apps** > **All apps**
2. Select an application you deployed in Module 05 (e.g., Microsoft 365 Apps or your LOB app)
3. Click on the app and select **Device install status** from the monitor section
4. Review the installation status for each device:
   - ✅ Installed
   - ⏳ Install pending
   - ❌ Failed
   - ℹ️ Not applicable
5. Filter by **Install status = Failed** to focus on problem devices
6. Click on a failed device to view the **Error code** and **Error description**
7. Note the error code and search the [Intune troubleshooting documentation](https://docs.microsoft.com/en-us/mem/intune/apps/troubleshoot-app-install)
8. Navigate to **Reports** > **App management** > **App install status report**
9. Click **Generate report** and filter by a specific app
10. Export the CSV and review in Excel for a broader view
11. Navigate to **Devices** > select a specific device > **App install status**
12. Review all apps assigned to that device and their installation states

**Expected Outcome**: You can track application deployment success rates per app and per device, and identify specific error codes for failed installations

---

## ✅ Best Practices

**Do's**:
- ✅ Review the compliance dashboard weekly and set up alerts for spikes in non-compliance
- ✅ Use Endpoint Analytics startup scores to proactively identify aging hardware
- ✅ Enable Log Analytics integration early — data is not backfilled
- ✅ Save frequently-used KQL queries in Log Analytics for quick reuse
- ✅ Export compliance reports before major policy changes as a baseline
- ✅ Use Proactive Remediations to auto-fix common issues before they escalate
- ✅ Set up compliance email notifications so users know when their device is non-compliant
- ✅ Share Endpoint Analytics reports with leadership as part of IT health dashboards

**Don'ts**:
- ❌ Don't rely solely on the overview dashboard — drill into per-device details for accuracy
- ❌ Don't ignore persistent app installation failures — they indicate a systemic issue
- ❌ Don't overlook audit logs — they're essential for change management and security investigations
- ❌ Don't skip the Log Analytics setup — built-in reports have limited retention
- ❌ Don't share exported reports externally without removing PII (usernames, device names)
- ❌ Don't use Endpoint Analytics scores as the only hardware refresh metric

---

## 🔧 Common Issues and Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| **Compliance report shows stale data** | Device hasn't checked in recently | Force sync from Intune portal; check device connectivity |
| **No data in Log Analytics** | Diagnostic settings not configured | Verify diagnostic settings are saved and data type is selected |
| **KQL query returns no results** | Data hasn't started flowing yet | Wait 15–30 mins after enabling; verify workspace is linked correctly |
| **Endpoint Analytics score is N/A** | Device doesn't meet requirements | Verify Windows 10 1903+ and Intune enrollment |
| **App installation status stuck at "Pending"** | Device offline or assignment not processed | Verify device is online, trigger sync, check assignment group |
| **Proactive Remediation not running** | Script syntax error or wrong device scope | Test script manually on a device; check assignment group |
| **Power BI OData feed fails to connect** | Authentication or permission issue | Ensure admin has Intune Service Administrator role; re-authenticate |

---

## 📝 Assessment Questions

1. **What is Endpoint Analytics and what types of scores does it provide?**
   <details>
   <summary>Answer</summary>
   Endpoint Analytics is a feature within Microsoft Intune (part of Microsoft Productivity Score) that measures device experience health. It provides scores for: Startup performance (boot and sign-in times), App reliability (crash/hang rates), Work from anywhere (cloud management readiness), Battery health (estimated vs design capacity), and Resource performance (CPU/RAM spikes).
   </details>

2. **Why should you enable Log Analytics integration early in your Intune deployment?**
   <details>
   <summary>Answer</summary>
   Log Analytics data is not backfilled — it only captures logs from the point it is enabled onwards. Enabling it early ensures you have historical data available for trend analysis, auditing, and troubleshooting. Built-in Intune reports also have limited data retention compared to a Log Analytics workspace.
   </details>

3. **Write a KQL query that shows the count of devices by compliance state.**
   <details>
   <summary>Answer</summary>

   ```kql
   IntuneDeviceComplianceOrg
   | summarize count() by ComplianceState
   ```
   </details>

4. **What is a Proactive Remediation in Endpoint Analytics and how does it work?**
   <details>
   <summary>Answer</summary>
   A Proactive Remediation is a script package consisting of a detection script and a remediation script. The detection script checks for a specific issue (exits with code 1 if the issue is detected). If the detection script signals an issue, the remediation script runs automatically to fix it. Results are reported back to Endpoint Analytics, showing which devices were detected with the issue and whether remediation was successful.
   </details>

5. **How would you monitor whether a deployed application is successfully installed across your device fleet?**
   <details>
   <summary>Answer</summary>
   Navigate to Intune > Apps > All apps, select the application, and open the Device install status or User install status report under the Monitor section. This shows per-device installation status (Installed, Pending, Failed, Not applicable) with error codes for failures. You can also use Reports > App management > App install status report for an organisation-wide view, and Log Analytics with the IntuneOperationalLogs table to query for failures programmatically.
   </details>

---

## 🔗 Key Resources

- [Intune Reports Overview](https://docs.microsoft.com/en-us/mem/intune/fundamentals/reports)
- [Endpoint Analytics Documentation](https://docs.microsoft.com/en-us/mem/analytics/overview)
- [Log Analytics Integration with Intune](https://docs.microsoft.com/en-us/mem/intune/fundamentals/review-logs-using-azure-monitor)
- [KQL Quick Reference](https://docs.microsoft.com/en-us/azure/data-explorer/kql-quick-reference)
- [Proactive Remediations](https://docs.microsoft.com/en-us/mem/analytics/proactive-remediations)
- [Intune Data Warehouse](https://docs.microsoft.com/en-us/mem/intune/developer/reports-nav-intune-data-warehouse)
- [Troubleshoot App Installations](https://docs.microsoft.com/en-us/mem/intune/apps/troubleshoot-app-install)

---

## ⏭️ Next Steps

After completing this module:
1. **Enable** Log Analytics diagnostic settings in your test tenant immediately
2. **Save** three to five KQL queries you find most useful
3. **Review** Endpoint Analytics scores and identify the top improvement opportunity
4. **Proceed** to [Module 09 – Integrations & Automation](../Module-09-Integrations-Automation/) to learn Windows Autopilot, PowerShell automation, and the Graph API

---

**Module Status**: Ready for Training
**Last Updated**: April 2026
**Duration**: 4–5 hours (including labs)

# Module 10: Best Practices & Real-World Scenarios

[![Module Status](https://img.shields.io/badge/Status-Active-success)](https://github.com)
[![Labs](https://img.shields.io/badge/Labs-4-blue)](README.md)
[![Duration](https://img.shields.io/badge/Duration-4--5%20hours-orange)](README.md)

---

## 📋 Module Overview

This module consolidates everything you have learned and focuses on real-world implementation patterns, naming conventions, governance, and common pitfalls. You will design scalable group structures, build phased deployment rings, troubleshoot application failures using Intune logs, and audit your security configuration — the same tasks performed by Intune administrators in enterprise environments every day.

---

## 🎯 Learning Objectives

By the end of this module, you will be able to:

- **Design** a scalable Azure AD group structure for Intune policy targeting
- **Define** a naming convention for policies, profiles, and groups
- **Build** a phased (ring-based) deployment strategy for updates and apps
- **Troubleshoot** Win32 application installation failures using IME logs
- **Audit** and document security policies and compliance posture
- **Identify** the top 10 common Intune mistakes and how to avoid them
- **Apply** governance principles to RBAC, policy conflict resolution, and change management
- **Create** runbook-style documentation for repeatable Intune tasks

---

## 📚 Key Topics

### 1. Naming Conventions

A consistent naming convention is essential for policy management at scale. Without it, you end up with hundreds of policies that nobody understands.

#### Recommended Format
```
[Platform]-[Type]-[Description]-[Scope]
```

#### Examples
| Object | Example Name |
|--------|-------------|
| Windows config profile | `WIN-CFG-WiFi-Corporate-All` |
| iOS config profile | `iOS-CFG-Email-Exchange-All` |
| Compliance policy | `WIN-COMP-Standard-Corporate` |
| App protection policy | `iOS-APP-Outlook-Corporate` |
| Conditional Access | `CA-001-RequireCompliant-M365` |
| Azure AD group – devices | `GRP-DEV-WIN-Corporate-All` |
| Azure AD group – users | `GRP-USR-Pilot-Ring1` |
| Update ring | `WIN-UPD-Ring1-Pilot` |

#### Platform Prefixes
- `WIN` – Windows 10/11
- `iOS` – iPhone/iPad
- `AND` – Android
- `MAC` – macOS
- `ALL` – Cross-platform

---

### 2. Group Structure Design

#### Principles
- Use **dynamic** Azure AD groups wherever possible — they self-maintain
- Layer groups: **All Devices** → **By Platform** → **By Role** → **By Pilot Ring**
- Avoid targeting individual users/devices directly in Intune policies
- Maintain separate groups for **user-targeting** (app protection, Conditional Access) vs **device-targeting** (config profiles, compliance)

#### Recommended Group Hierarchy
```
GRP-DEV-ALL-ManagedDevices          ← all enrolled devices
├── GRP-DEV-WIN-All                 ← all Windows devices
│   ├── GRP-DEV-WIN-Corporate       ← corporate-owned Windows
│   └── GRP-DEV-WIN-BYOD            ← personal Windows
├── GRP-DEV-iOS-All
├── GRP-DEV-Android-All
└── GRP-DEV-MAC-All

GRP-USR-ALL-IntuneUsers             ← all users with Intune license
├── GRP-USR-Pilot-Ring1             ← IT staff / early adopters
├── GRP-USR-Pilot-Ring2             ← department leads
└── GRP-USR-Broad                   ← all remaining users
```

#### Dynamic Group Rules (examples)
```
# All Windows devices:
(device.deviceOSType -eq "Windows")

# Corporate-owned devices:
(device.deviceOwnership -eq "Company")

# Users in IT department:
(user.department -eq "Information Technology")
```

---

### 3. Phased Deployment Strategy

Never deploy changes to all devices at once. Use deployment rings to limit blast radius.

#### Ring Structure
| Ring | Who | Size | Purpose |
|------|-----|------|---------|
| **Ring 0 – IT Pilot** | IT admins | 5–10 users | Validate before anyone else |
| **Ring 1 – Early Adopters** | Power users, volunteers | 5–10% | Catch broad issues |
| **Ring 2 – Broad** | All remaining users | 90%+ | Full rollout after validation |

#### Rollout Timeline
```
Day 0:    Deploy to Ring 0 (IT Pilot)
Day 3-5:  Review Ring 0 results, fix issues
Day 7:    Deploy to Ring 1 (Early Adopters)
Day 14:   Review Ring 1 results, fix issues
Day 21:   Deploy to Ring 2 (Broad)
Day 30:   Post-deployment review
```

#### Windows Update Rings
Apply the same ring concept to Windows Update for Business:
- Ring 0: 0 day deferral (gets updates immediately)
- Ring 1: 7 day deferral
- Ring 2: 14–21 day deferral

---

### 4. Policy Conflict Resolution

When two policies configure the same setting differently, Intune applies conflict resolution rules:

#### Conflict Behaviour by Policy Type
| Policy Type | Conflict Result |
|------------|----------------|
| **Compliance policies** | Most restrictive wins |
| **Configuration profiles** | "Error" — conflict is flagged, neither applies |
| **Security baselines** | Conflict flagged, check per-setting report |
| **App protection policies** | Most restrictive wins |

#### How to Find Conflicts
1. Navigate to **Devices** > select a device > **Device configuration**
2. Look for profiles with **Conflict** status
3. Click the profile > **Per-setting status** to identify the conflicting setting
4. Remove or reconcile the overlapping policy

---

### 5. Top 10 Common Intune Mistakes

1. **Targeting all users/all devices without piloting first** — always start with a pilot group
2. **No naming convention** — creates management chaos at scale
3. **Forgetting break-glass accounts in Conditional Access** — can lock out all admins
4. **Enabling BitLocker without verifying recovery key escrow** — risk of data loss
5. **Deploying Win32 apps without testing the IntuneWinAppUtil package** — silent failures
6. **Ignoring policy conflicts** — settings silently stop applying
7. **Not testing compliance policies before enabling Conditional Access** — user disruption
8. **Over-assigning Required apps** — Company Portal flooded, poor user experience
9. **Skipping Endpoint Analytics** — miss hardware aging and performance issues
10. **No documentation or change log** — makes audits and troubleshooting painful

---

### 6. RBAC and Governance

#### Built-In Intune Roles
| Role | Capabilities |
|------|-------------|
| **Intune Service Administrator** | Full Intune access |
| **Help Desk Operator** | View devices, remote actions (no policy changes) |
| **Read Only Operator** | View-only, no changes |
| **Policy and Profile Manager** | Manage config profiles and compliance, no app management |
| **Application Manager** | Manage apps only |

#### Governance Principles
- Apply **least privilege** — assign the minimum role needed
- Use **custom roles** for tailored scopes (e.g., only manage iOS devices)
- Use **scope tags** to limit what each admin can see and manage
- Review role assignments quarterly

---

## 🧪 Hands-On Labs

---

### Lab 10.1: Design an Effective Group Structure

**Objective**: Create a scalable Azure AD group hierarchy with dynamic rules for Intune targeting

**Steps**:

1. Sign in to [https://entra.microsoft.com](https://entra.microsoft.com)
2. Navigate to **Groups** > **All groups** > **+ New group**
3. Create the following dynamic device groups (Group type: Security, Membership: Dynamic device):

   **Group 1**: `GRP-DEV-WIN-Corporate`
   - Dynamic rule: `(device.deviceOSType -eq "Windows") and (device.deviceOwnership -eq "Company")`

   **Group 2**: `GRP-DEV-iOS-All`
   - Dynamic rule: `(device.deviceOSType -eq "iPhone") or (device.deviceOSType -eq "iPad")`

   **Group 3**: `GRP-DEV-Android-All`
   - Dynamic rule: `(device.deviceOSType -eq "Android")`

4. Create the following dynamic user groups (Membership: Dynamic user):

   **Group 4**: `GRP-USR-Pilot-Ring1`
   - Dynamic rule: `(user.department -eq "Information Technology")`

   **Group 5**: `GRP-USR-Pilot-Ring2`
   - Dynamic rule: `(user.jobTitle -contains "Manager")`

5. For each group, click **Validate rules** and test with a sample user or device
6. Wait for dynamic group membership to evaluate (can take up to 5 minutes)
7. Navigate to each group and verify **Members** are populated correctly
8. Document your group structure in a table with group name, type, dynamic rule, and purpose

**Expected Outcome**: You have a structured group hierarchy that automatically populates based on device and user properties, ready for policy assignment

---

### Lab 10.2: Build a Phased Deployment Strategy

**Objective**: Configure Windows Update rings and app deployment with pilot and broad rings

**Steps**:

1. In Intune, navigate to **Devices** > **Windows** > **Update rings for Windows 10 and later**
2. Create **Ring 0 – IT Pilot**:
   - Click **+ Create**
   - Name: `WIN-UPD-Ring0-ITPilot`
   - Feature update deferral: **0 days**
   - Quality update deferral: **0 days**
   - Assign to: `GRP-USR-Pilot-Ring1` (IT group)
3. Create **Ring 1 – Early Adopters**:
   - Name: `WIN-UPD-Ring1-EarlyAdopters`
   - Feature update deferral: **30 days**
   - Quality update deferral: **7 days**
   - Assign to: `GRP-USR-Pilot-Ring2`
4. Create **Ring 2 – Broad**:
   - Name: `WIN-UPD-Ring2-Broad`
   - Feature update deferral: **60 days**
   - Quality update deferral: **14 days**
   - Assign to: `GRP-DEV-WIN-Corporate` (exclude Ring 0 and Ring 1 groups)
5. Navigate to **Apps** > select an application > **Properties** > **Assignments**
6. Add assignment: Group = `GRP-USR-Pilot-Ring1`, Intent = **Required**, schedule = available now
7. Add a second assignment: Group = `GRP-USR-Pilot-Ring2`, Intent = **Required**, with a **deadline** 7 days out
8. Document the rollout plan: ring sizes, deferral periods, and escalation criteria

**Expected Outcome**: Update rings create staggered Windows Update delivery; app assignments enforce pilot-then-broad rollout

---

### Lab 10.3: Troubleshoot an Application Installation Failure

**Objective**: Use Intune logs and the IME (Intune Management Extension) to diagnose a failed Win32 app installation

**Steps**:

1. In Intune, navigate to **Apps** > **All apps** and identify an app showing **Failed** status on at least one device
2. Click the app > **Device install status** > click on the failed device
3. Note the **Error code** and **Error description** shown in the portal
4. On the affected Windows device, open **Event Viewer**:
   - Navigate to: **Applications and Services Logs** > **Microsoft** > **Windows** > **DeviceManagement-Enterprise-Diagnostics-Provider** > **Admin**
   - Filter for **Error** level events related to the app
5. On the device, navigate to: `C:\ProgramData\Microsoft\IntuneManagementExtension\Logs\`
6. Open `IntuneManagementExtension.log` in a text editor (or use CMTrace/OneTrace)
7. Search for the application name — look for lines with `Failed` or error codes
8. Common error codes to look up:
   - `0x87D1041C` — App not detected after install (detection rule issue)
   - `0x80070005` — Access denied (permissions issue)
   - `0x80070032` — App already installed (conflict)
9. Navigate to **Devices** > select the device > **Collect diagnostics** to gather a full diagnostic bundle
10. Review the diagnostic ZIP for additional logs
11. Based on your findings, document: root cause, fix applied, and verification steps

**Expected Outcome**: You can locate and interpret IME logs to identify the root cause of Win32 app installation failures

---

### Lab 10.4: Review and Audit Security Policies

**Objective**: Generate a compliance and security audit report, identify gaps, and document a remediation plan

**Steps**:

1. In Intune, navigate to **Reports** > **Device compliance** > **Compliance report (Organisational)**
2. Click **Generate report** — note the percentage of compliant, non-compliant, and not-evaluated devices
3. Navigate to **Endpoint security** > **Security baselines**
4. Click on your deployed baseline (from Module 06 Lab 6.6) > **Version profiles** > click your profile
5. Click **Device status** — review devices that are **Succeeded**, **Error**, or **Conflict**
6. Click **Per-setting status** — identify any settings with a high number of errors or conflicts
7. Navigate to **Endpoint security** > **Conditional Access** — review all CA policies
8. For each CA policy, verify:
   - Does it exclude a break-glass account?
   - Is it scoped to the correct users and apps?
   - Is it in Report-only or Enabled mode?
9. Navigate to **Tenant administration** > **Audit logs**
10. Filter by **Date range = last 7 days** and review admin activity
11. Export the audit log as CSV
12. Create a simple audit document covering:
    - Compliance rate (%)
    - Number of non-compliant devices and top failing settings
    - Security baseline conflicts
    - CA policy review findings
    - Recommended actions

**Expected Outcome**: You have a structured security audit report that could be presented to a security team or management

---

## ✅ Best Practices

**Do's**:
- ✅ Establish a naming convention **before** you create your first policy
- ✅ Use dynamic Azure AD groups to eliminate manual group management
- ✅ Always pilot with Ring 0 (IT) before broader deployment
- ✅ Document every policy with purpose, owner, and change history
- ✅ Review role assignments and scope tags quarterly
- ✅ Use scope tags to give regional admins visibility only into their devices
- ✅ Keep a change log for every Intune policy modification
- ✅ Run the compliance audit report monthly and track improvement over time

**Don'ts**:
- ❌ Don't create policies without a documented purpose
- ❌ Don't assign the same setting in multiple policies (causes conflicts)
- ❌ Don't give all IT staff the Intune Service Administrator role — use least privilege
- ❌ Don't deploy to all users/devices without piloting first
- ❌ Don't ignore policy conflict warnings — they mean a setting is not being applied
- ❌ Don't rename policies after creating them — it breaks audit trail clarity
- ❌ Don't skip the break-glass exclusion in Conditional Access
- ❌ Don't forget to update group assignments when organisational structure changes

---

## 🔧 Common Issues and Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| **Dynamic group not populating** | Rule syntax error or attribute not set on objects | Use "Validate rules" in Azure AD; verify attribute is populated on users/devices |
| **Policy conflict – setting not applying** | Two profiles assign the same setting | Use per-setting status to find conflicting profiles; consolidate or remove one |
| **Win32 app shows "Not Applicable"** | Detection rule not matching installed state | Review and update detection script or registry key path |
| **Update ring not applying** | Device not in assigned group | Verify device dynamic group membership; force sync |
| **Audit log missing expected changes** | Log filtered out or viewer lacks permission** | Adjust date filter; verify admin has Audit log read permission |
| **RBAC scope tag blocking admin** | Admin has scope tag that excludes the device | Review scope tag assignment for admin role |
| **App required but not installing** | Assignment intent set to Available instead of Required | Edit assignment and change intent to Required |

---

## 📝 Assessment Questions

1. **What is the recommended naming convention pattern for Intune policies and why is it important?**
   <details>
   <summary>Answer</summary>
   A recommended pattern is [Platform]-[Type]-[Description]-[Scope], e.g. WIN-CFG-WiFi-Corporate-All. Naming conventions are important because they make it immediately clear what a policy does, which platform it applies to, and what it targets — without having to open it. This is critical when managing hundreds of policies and is essential for audits, troubleshooting, and onboarding new admins.
   </details>

2. **Why should you use dynamic Azure AD groups instead of assigned (static) groups for Intune targeting?**
   <details>
   <summary>Answer</summary>
   Dynamic groups automatically add or remove members based on device or user attributes (OS type, department, ownership, etc.). This eliminates manual maintenance, reduces human error, and ensures that new devices and users are automatically covered by the right policies without admin intervention.
   </details>

3. **Describe the three-ring deployment model and the purpose of each ring.**
   <details>
   <summary>Answer</summary>
   Ring 0 (IT Pilot): A small group of IT admins who receive changes first to validate them with no impact on end users. Ring 1 (Early Adopters): A broader group of power users or volunteers (~5–10%) who catch issues that didn't surface in Ring 0. Ring 2 (Broad): All remaining users, receiving the change after it has been validated by the first two rings. Each ring provides a gate to catch problems before they affect a larger audience.
   </details>

4. **What happens when two Intune configuration profiles assign conflicting values to the same setting?**
   <details>
   <summary>Answer</summary>
   For configuration profiles, a conflict is flagged and neither value is applied to the device — the setting is left in its previous state. The conflict appears in the device's configuration profile status. This is different from compliance policies and app protection policies, where the most restrictive value wins. Admins must resolve configuration profile conflicts by removing or reconciling the overlapping setting.
   </details>

5. **What logs should you check first when a Win32 application fails to install via Intune?**
   <details>
   <summary>Answer</summary>
   First check the Intune portal for the error code under Apps > [app] > Device install status. On the device, check: (1) C:\ProgramData\Microsoft\IntuneManagementExtension\Logs\IntuneManagementExtension.log for IME-level errors, and (2) Event Viewer under Applications and Services Logs > Microsoft > Windows > DeviceManagement-Enterprise-Diagnostics-Provider > Admin. You can also use Collect diagnostics from the Intune portal to gather a full bundle.
   </details>

---

## 🔗 Key Resources

- [Intune RBAC – Built-In Roles](https://docs.microsoft.com/en-us/mem/intune/fundamentals/role-based-access-control)
- [Windows Update Rings in Intune](https://docs.microsoft.com/en-us/mem/intune/protect/windows-update-for-business-configure)
- [Dynamic Group Rules – Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/enterprise-users/groups-dynamic-membership)
- [Troubleshoot Win32 App Deployments](https://docs.microsoft.com/en-us/mem/intune/apps/troubleshoot-win32-app-install)
- [Intune Policy Conflicts](https://docs.microsoft.com/en-us/mem/intune/configuration/device-profile-troubleshoot#conflicts)
- [Scope Tags in Intune](https://docs.microsoft.com/en-us/mem/intune/fundamentals/scope-tags)
- [Intune Audit Logs](https://docs.microsoft.com/en-us/mem/intune/fundamentals/monitor-audit-logs)

---

## ⏭️ Next Steps

After completing this module:
1. **Apply** naming conventions retroactively to any policies created during this course
2. **Create** the recommended group structure in your test tenant
3. **Build** Windows Update rings for your pilot and broad deployment groups
4. **Proceed** to [Module 11 – Capstone Project & Certification](../Module-11-Capstone/) to apply everything in a comprehensive enterprise scenario

---

**Module Status**: Ready for Training
**Last Updated**: April 2026
**Duration**: 4–5 hours (including labs)

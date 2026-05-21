# Module 05: Application Management

## Module Overview
This module focuses on managing applications across your organization using Microsoft Intune. You'll learn how to deploy, configure, and manage applications on Windows, macOS, iOS, and Android devices. This includes managing Microsoft 365 apps, line-of-business applications, and web applications.

---

## Learning Objectives
By the end of this module, you will be able to:

- **Understand** application management capabilities in Intune
- **Add** applications from various sources (Microsoft Store, LOB apps, web apps)
- **Configure** application assignments and deployment options
- **Manage** application Versions and updates
- **Monitor** application deployment and health
- **Apply** protection policies for sensitive applications
- **Handle** application dependencies and prerequisites
- **Troubleshoot** common application deployment issues

---

## Key Topics

### 1. Application Management Fundamentals
- **What is Application Management in Intune?**
  - Centralized application deployment and management
  - Support for multiple platforms and device types
  - Application lifecycle management
  - License management and compliance

- **Types of Applications Supported:**
  - Microsoft Store apps
  - Microsoft 365 apps
  - Line-of-Business (LOB) applications
  - Web applications
  - Built-in apps
  - Managed apps

### 2. Adding Applications to Intune

#### Microsoft Store Apps
- Browse and add apps directly from Microsoft Store
- Configure app availability (available or required)
- Manage app versions and updates

#### Line-of-Business (LOB) Applications
- Upload custom `.exe`, `.msi`, or `.msix` files
- Configure installer command lines
- Set prerequisites and dependencies
- Manage app icons and descriptions

#### Microsoft 365 Applications
- Deploy Office 365 suite
- Configure installation preferences
- Manage update channels
- Control which apps are installed

#### Web Applications
- Deploy as web links/shortcuts
- Target specific browsers
- Manage homepage configurations

#### iOS and Android Applications
- Deploy from Apple App Store and Google Play Store
- Managed app policies
- Configure app protection settings
- Handle app store enrollment

### 3. Application Assignment and Deployment

#### Assignment Groups
- Assign to user groups
- Assign to device groups
- Evaluate group membership dynamically

#### Deployment Options
- **Available**: Users can install from Company Portal
- **Required**: Application is automatically installed
- **Uninstall**: Remove application from devices
- **Conditional Access**: Advanced targeting scenarios

#### Deployment Filters
- Filter by device ownership (corporate/personal)
- Filter by OS version
- Filter by device categories
- Custom filters based on device properties

### 4. Application Configuration

#### App Configuration Policies
- Managed and unmanaged app configurations
- JSON-based app settings
- Pre-configure application behavior
- Deploy different configs to different user groups

#### Application Protection Policies (APP)
- Conditional launch settings
- Offline grace periods
- Device compliance requirements
- Data transfer restrictions
- Copy/paste controls
- Screen capture restrictions

### 5. Office 365 ProPlus Management

#### Installation and Configuration
- Configure XML-based installation profiles
- Deploy specific Office apps
- Manage update channels (Current, Monthly, Semi-Annual)
- Set activation and licensing options

#### Office Updates
- Auto-update management
- Update deadlines
- Version pinning
- Cloud update services

### 6. Application Monitoring and Reporting

#### Application Deployment Status
- Monitor installation success/failure
- View real-time deployment progress
- Identify devices with errors
- Troubleshoot failed installations

#### Key Metrics
- Installation status by device
- Application health status
- User feedback and ratings
- License usage tracking

#### Reports Available
- Application deployment status
- Device compliance status
- Protected apps report
- App protection status

### 7. Common Deployment Scenarios

#### Scenario 1: Deploy Line-of-Business Application
1. Prepare application package and installer
2. Add LOB app to Intune
3. Configure installation settings
4. Assign to user/device groups
5. Monitor deployment progress

#### Scenario 2: Deploy Microsoft 365 with Specific Configuration
1. Create Microsoft 365 app deployment
2. Configure installation preferences
3. Set update channel and update deadline
4. Assign to corporate devices
5. Monitor installation and licensing

#### Scenario 3: Deploy Web Application as Shortcut
1. Add web application in Intune
2. Configure app URL
3. Set app icon
4. Assign to user groups
5. Verify in Company Portal

#### Scenario 4: Managed App Protection
1. Create app protection policy
2. Configure conditional launch settings
3. Set data protection rules
4. Assign to managed apps
5. Monitor policy compliance

### 8. Application Dependencies and Prerequisites

#### Managing Dependencies
- Set required apps that must be installed first
- Configure installation order
- Handle version conflicts
- Monitor dependency installation status

#### Prerequisites Configuration
- OS version requirements
- Minimum hardware requirements
- Antivirus software detection
- Architecture-specific requirements (x86, x64, ARM64)

---

## Hands-On Labs

### Lab 1: Add a Line-of-Business Application
**Objective**: Deploy a custom LOB application to end users

**Steps**:
1. Navigate to Intune → Apps → All apps
2. Click "Add" and select app type "Windows"
3. Upload your LOB application file (.msi or .exe)
4. Configure:
   - App name and description
   - Publisher information
   - App icon
   - Install command line
   - Uninstall command line
5. Click "Next" and assign to a test group
6. Review and create
7. Monitor deployment status from a test device

**Expected Outcome**: Application appears in Company Portal and installs on assigned devices

---

### Lab 2: Deploy and Configure Microsoft 365 Applications
**Objective**: Deploy Microsoft 365 apps with specific configuration

**Steps**:
1. Go to Intune → Apps → All apps
2. Click "Add" and search for "Microsoft 365 Apps"
3. Configure:
   - Select apps to include (Word, Excel, PowerPoint, etc.)
   - Update channel (Current Channel, Monthly, etc.)
   - Set update deadline
   - Configure on device experience
   - Assign app licensing model
4. Assign to device group
5. Create deployment profile with custom XML (if needed)
6. Monitor installation and activation status

**Expected Outcome**: Office 365 apps deploy according to configuration; users can activate with organizational account

---

### Lab 3: Create Application Protection Policy
**Objective**: Enforce data protection policies on managed applications

**Steps**:
1. Navigate to Intune → Apps → App protection policies
2. Click "Create policy"
3. Select platform (iOS or Android)
4. Configure:
   - Conditional launch requirements
   - Data protection rules
   - Access requirements (PIN, biometric)
   - Device compliance requirements
5. Select apps to protect
6. Assign to user groups
7. Verify policy enforcement on test device

**Expected Outcome**: Protected apps enforce policy requirements; unauthorized access is blocked

---

### Lab 4: Deploy Web Application
**Objective**: Deploy web application as a shortcut in Company Portal

**Steps**:
1. In Intune → Apps → All apps
2. Click "Add" and select "Web app"
3. Configure:
   - App name
   - App description
   - App URL (e.g., https://portal.office.com)
   - App icon URL
   - Category
4. Assign to user group
5. Have user access Company Portal and verify app appears
6. Click app shortcut and verify it launches in browser

**Expected Outcome**: Web app appears as tile in Company Portal; clicking launches application

---

### Lab 5: Monitor Application Deployment
**Objective**: Review application deployment status and troubleshoot failures

**Steps**:
1. Navigate to Intune → Apps → All apps
2. Select an application you've deployed
3. Review:
   - Overall installation status
   - Device installation status (Success/Pending/Failed)
   - Device groups receiving the app
   - Installation status by OS version
4. Click on failed installation to view error details
5. Document common failure reasons
6. Test remediation steps

**Expected Outcome**: Understand deployment status reports and identify troubleshooting approaches

---

## Best Practices

✅ **Do's**:
- Test applications in pilot groups before broad rollout
- Use available (not required) deployments when possible
- Configure app protection policies for sensitive apps
- Monitor application health regularly
- Document application dependencies
- Keep application versions up-to-date
- Use deployment filters for targeting
- Track licensing and compliance

❌ **Don'ts**:
- Deploy untested applications to production
- Require all applications for all users
- Ignore application protection policy alerts
- Deploy applications without prerequisites installed
- Mix personal and work app policies
- Disable updates without proper planning
- Deploy conflicting application versions
- Ignore failed deployment notifications

---

## Common Issues and Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| **App installation fails** | Missing prerequisites, system requirements not met | Check prerequisites, verify OS version and hardware specs |
| **App not appearing in Company Portal** | User not in assignment group, device not synced | Verify group assignment, force device sync |
| **Office 365 activation fails** | Licensing issue, invalid credentials | Verify license assignment, check user authentication |
| **App update stuck pending** | Device offline, insufficient disk space | Ensure device connectivity, check available storage |
| **App protection policy not enforcing** | Device not enrolled, policy not assigned correctly | Verify device enrollment, check policy assignment |
| **LOB app won't uninstall** | No uninstall command configured | Add uninstall command line to app configuration |

---

## Assessment Questions

1. **What are the four main types of applications you can manage in Intune?**
   <details>
   <summary>Answer</summary>
   Microsoft Store apps, Line-of-Business apps, Microsoft 365 apps, and Web applications.
   </details>

2. **Describe the difference between "Available" and "Required" application assignments.**
   <details>
   <summary>Answer</summary>
   Available: Users can choose to install from Company Portal. Required: Application automatically installs on assigned devices.
   </details>

3. **What is an Application Protection Policy (APP) and when would you use it?**
   <details>
   <summary>Answer</summary>
   APP enforces data protection rules on managed applications without requiring full device enrollment. Use for BYOD or when protecting sensitive apps.
   </details>

4. **How would you ensure a LOB application installs in the correct order if it depends on another application?**
   <details>
   <summary>Answer</summary>
   Configure application dependencies in Intune app settings and ensure prerequisite apps are assigned first.
   </details>

5. **What should you do if an application deployment fails on certain devices?**
   <details>
   <summary>Answer</summary>
   Review deployment status reports, check error logs on failed devices, verify prerequisites are met, and test in a pilot group before troubleshooting.
   </details>

---

## Key Resources

- [Microsoft Intune App Management Documentation](https://docs.microsoft.com/intune/apps/)
- [Intune Application Protection Overview](https://docs.microsoft.com/intune/app-protection-overview)
- [Deploy Microsoft 365 Apps](https://docs.microsoft.com/intune/apps/apps-add-office-apps)
- [LOB App Deployment Guide](https://docs.microsoft.com/intune/apps/lob-apps-windows)
- [Intune Company Portal](https://docs.microsoft.com/intune/user-end-user-company-portal)

---

## Next Steps

After completing this module:
1. **Practice** deploying applications in your test environment
2. **Create** application deployment plans for your organization
3. **Develop** standardized processes for application management
4. **Proceed** to Module-06-Security-Compliance for security-focused management strategies

---

**Module Status**: Ready for Training
**Last Updated**: February 2026
**Duration**: 4-6 hours (including labs)

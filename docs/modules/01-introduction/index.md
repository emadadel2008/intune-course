# Module 1 - Introduction to Microsoft Intune

## 📋 Module Overview

Welcome to Module 1! This module introduces you to Microsoft Intune and Modern Device Management concepts. You'll learn what Intune is, why it's essential for modern organizations, and get hands-on experience exploring the Microsoft Endpoint Manager admin center.

## 🎯 Learning Objectives

By the end of this module, you will be able to:
- Explain what Microsoft Intune is and its role in device management
- Understand the evolution from traditional to modern device management
- Navigate the Microsoft Endpoint Manager admin center
- Identify common Intune use cases in enterprise environments
- Recognize the benefits of cloud-based device management

## 📚 Module Content

### 1.1 What is Microsoft Intune?

Microsoft Intune is a cloud-based service that focuses on mobile device management (MDM) and mobile application management (MAM). It enables organizations to:

- **Manage Devices**: Control how devices access organizational resources
- **Secure Data**: Protect company data on personal and corporate devices
- **Deploy Applications**: Distribute and manage apps across all platforms
- **Enforce Policies**: Apply security and compliance policies automatically
- **Enable Productivity**: Allow users to work securely from anywhere

#### Key Features
- Cross-platform device management (Windows, iOS, Android, macOS)
- Application lifecycle management
- Conditional access policies
- Integration with Microsoft 365 and Azure AD
- Endpoint security and compliance
- Remote device actions

### 1.2 Understanding Modern Device Management

#### Traditional vs Modern Management

| Aspect | Traditional (On-Premises) | Modern (Cloud-Based) |
|--------|---------------------------|----------------------|
| **Infrastructure** | Requires servers, SCCM | Cloud-native, no servers |
| **Device Types** | Primarily Windows PCs | All platforms (BYOD support) |
| **Management Style** | Device-centric | User-centric |
| **Network Dependency** | Requires VPN/domain connection | Works from anywhere |
| **Deployment Speed** | Days to weeks | Minutes to hours |
| **Cost Structure** | High upfront CAPEX | Subscription-based OPEX |

#### Why Modern Device Management?

1. **Remote Workforce**: Users work from anywhere, anytime
2. **BYOD Trend**: Employees use personal devices for work
3. **Cloud-First Strategy**: Organizations moving to cloud services
4. **Security Requirements**: Need for zero-trust security models
5. **Rapid Deployment**: Faster device provisioning and updates

### 1.3 Microsoft Intune Architecture

```
┌─────────────────────────────────────────────────────┐
│           Microsoft Endpoint Manager                 │
│  (Admin Portal - endpoint.microsoft.com)            │
└──────────────────┬──────────────────────────────────┘
                   │
        ┌──────────┴──────────┐
        │                     │
┌───────▼────────┐   ┌────────▼────────┐
│  Intune MDM    │   │  Intune MAM     │
│  (Devices)     │   │  (Apps Only)    │
└───────┬────────┘   └────────┬────────┘
        │                     │
        └──────────┬──────────┘
                   │
    ┌──────────────┼──────────────┐
    │              │              │
┌───▼───┐   ┌─────▼─────┐   ┌───▼───┐
│Windows│   │iOS/Android│   │ macOS │
└───────┘   └───────────┘   └───────┘
```

### 1.4 Integration with Microsoft Ecosystem

- **Azure Active Directory**: Identity and authentication
- **Microsoft 365**: Office apps and services integration
- **Microsoft Defender**: Advanced threat protection
- **Autopilot**: Zero-touch device deployment
- **Purview**: Information protection and compliance

## 🔬 Hands-On Labs

### Lab 1.1: Explore Microsoft Endpoint Manager Admin Center

**Objective**: Familiarize yourself with the Intune admin interface and its main components.

**Prerequisites**:
- Microsoft 365 account (can use trial)
- Internet browser (Edge or Chrome recommended)
- Basic understanding of cloud services

**Estimated Time**: 30 minutes

#### Step-by-Step Instructions

**Step 1: Access the Admin Center**

1. Open your web browser
2. Navigate to: `https://endpoint.microsoft.com`
3. Sign in with your Microsoft 365 administrator credentials
4. You'll be directed to the Microsoft Endpoint Manager admin center home page

**Step 2: Explore the Dashboard**

1. **Home Dashboard**:
   - Review the overview widgets showing device status
   - Note the quick action tiles
   - Observe any alerts or notifications

2. **Navigation Menu** (Left Sidebar):
   - Locate the main menu sections:
     - Home
     - Dashboard
     - Devices
     - Apps
     - Users
     - Groups
     - Tenant administration
     - Troubleshooting + support
     - Reports

**Step 3: Explore Devices Section**

1. Click on **Devices** in the left menu
2. Explore the following sub-sections:
   - **Overview**: Summary of all managed devices
   - **All devices**: List view of enrolled devices
   - **Windows**: Windows-specific management
   - **iOS/iPadOS**: Apple device management
   - **Android**: Android device management
   - **macOS**: Mac computer management
   - **Configuration profiles**: Device settings
   - **Compliance policies**: Security requirements
   - **Conditional Access**: Access control policies

3. Click on **Configuration profiles**
   - Note the "Create profile" button
   - Review available platform options
   - Observe the different profile types available

**Step 4: Explore Apps Section**

1. Click on **Apps** in the left menu
2. Review:
   - **Overview**: App deployment summary
   - **All apps**: List of managed applications
   - **Windows apps**: Platform-specific apps
   - **iOS apps**: iPhone/iPad applications
   - **Android apps**: Android applications
   - **App protection policies**: MAM policies
   - **App configuration policies**: App settings

**Step 5: Explore Users and Groups**

1. Click on **Users**
   - This links to Azure AD users
   - Review user list and details
   
2. Click on **Groups**
   - View existing groups
   - Note group types (Assigned vs Dynamic)
   - Understand group-based policy assignment

**Step 6: Review Tenant Administration**

1. Click on **Tenant administration**
2. Explore:
   - **Tenant status**: Overall health
   - **Connectors and tokens**: Third-party integrations
   - **Roles**: Administrative permissions
   - **Customization**: Company branding
   - **Intune Data warehouse**: Reporting data

**Step 7: Explore Reports Section**

1. Click on **Reports**
2. Review available report categories:
   - Device compliance
   - Device enrollment
   - Software updates
   - App usage
   - Endpoint analytics

**Step 8: Use the Search Feature**

1. Use the search bar at the top
2. Try searching for:
   - "Compliance"
   - "BitLocker"
   - "Conditional Access"
3. Notice how it provides quick navigation

#### Verification

✅ You can navigate to different sections of the admin center  
✅ You understand the main menu structure  
✅ You've explored Devices, Apps, and Reports sections  
✅ You can use the search functionality

#### Lab Questions

1. Where would you go to create a new device configuration profile?
2. Which section shows you the compliance status of all devices?
3. How do you access Azure AD user management from Intune?
4. Where can you view reports about app installations?

---

### Lab 1.2: Review Intune Use Case Scenarios

**Objective**: Understand real-world applications of Microsoft Intune across different business scenarios.

**Prerequisites**:
- Completed Lab 1.1
- Access to Microsoft Endpoint Manager admin center

**Estimated Time**: 45 minutes

#### Scenario-Based Exploration

#### **Scenario 1: BYOD (Bring Your Own Device)**

**Business Need**: Employees want to access company email on their personal smartphones without giving IT full control of their device.

**Solution Walkthrough**:

1. Navigate to **Apps** > **App protection policies**
2. Click **Create policy** and select **iOS/iPadOS** or **Android**
3. Review the policy creation wizard:
   - **Apps**: Select which apps to protect (e.g., Outlook, Teams)
   - **Data protection**: Control copy/paste, backup, encryption
   - **Access requirements**: PIN, biometric authentication
   - **Conditional launch**: Block jailbroken devices

4. Key Benefits:
   - Protects company data without managing entire device
   - User privacy maintained (IT can't wipe personal data)
   - Compliance without intrusion

**Real-World Example**:
```
Contoso Ltd. has 500 employees who use personal phones. 
With MAM policies, they can:
- Access Outlook and Teams securely
- Prevent data leakage via copy/paste
- Require app PIN after 30 mins idle
- Block access from jailbroken devices
```

#### **Scenario 2: Corporate Device Deployment**

**Business Need**: Company purchases 100 new Windows laptops for employees and needs to configure them quickly with corporate standards.

**Solution Walkthrough**:

1. Navigate to **Devices** > **Windows** > **Windows enrollment**
2. Explore **Windows Autopilot**:
   - Zero-touch deployment
   - Devices ship directly to users
   - Automatic configuration on first boot

3. Review Configuration Profiles:
   - Navigate to **Devices** > **Configuration profiles**
   - Examine profile templates:
     - **Device restrictions**: Control Windows features
     - **Wi-Fi**: Automatic Wi-Fi connection
     - **VPN**: Corporate network access
     - **Email**: Configure Outlook automatically
     - **Endpoint protection**: Antivirus settings

4. Click on **Compliance policies**:
   - Examine compliance requirements:
     - BitLocker encryption enabled
     - Firewall turned on
     - Antivirus up-to-date
     - Password complexity requirements

**Real-World Example**:
```
Fabrikam purchases 100 Surface laptops:
1. Add device serial numbers to Autopilot
2. Ship directly to employee homes
3. Employee unboxes and powers on
4. Autopilot automatically:
   - Joins Azure AD
   - Enrolls in Intune
   - Applies all configurations
   - Installs required apps
5. Ready to use in 30 minutes!
```

#### **Scenario 3: Remote Workforce Security**

**Business Need**: Sales team travels frequently and accesses sensitive customer data from various locations.

**Solution Walkthrough**:

1. Navigate to **Endpoint security** > **Conditional Access**
2. Review Conditional Access policies:
   - Block access from risky locations
   - Require MFA for sensitive apps
   - Block non-compliant devices
   - Require managed devices

3. Explore **Endpoint security** > **Security baselines**:
   - MDM Security Baseline
   - Microsoft Defender for Endpoint
   - Microsoft Edge baseline

4. Review **Device actions**:
   - Navigate to **Devices** > **All devices**
   - Select a device and review available actions:
     - Remote lock
     - Wipe
     - Retire
     - Restart
     - Fresh Start

**Real-World Example**:
```
Adventure Works sales team:
- Must use compliant devices to access CRM
- MFA required when outside office network
- Device auto-locks after 5 mins idle
- Remote wipe available if device lost
- BitLocker protects data at rest
```

#### **Scenario 4: Application Management**

**Business Need**: IT needs to deploy and update business applications across 1,000 devices automatically.

**Solution Walkthrough**:

1. Navigate to **Apps** > **All apps**
2. Review app types available:
   - **Microsoft Store apps**: Consumer apps
   - **Microsoft 365 Apps**: Office suite
   - **Windows apps (Win32)**: Custom LOB apps
   - **Web links**: Browser-based apps
   - **Built-in apps**: Windows built-ins

3. Click **Add** and explore app deployment:
   - Select app type
   - Configure app information
   - Assign to groups (Required vs Available)
   - Set detection rules
   - Configure update behavior

4. Review **App protection policies**:
   - Protect data within apps
   - Control data transfer between apps
   - Enforce encryption

**Real-World Example**:
```
Northwind Traders needs to deploy:
- Microsoft 365 Apps (Required for all)
- Adobe Acrobat (Required for Finance dept)
- Custom CRM app (Available for Sales)
- Slack (Optional for all)

Intune automatically:
- Installs required apps during enrollment
- Makes available apps visible in Company Portal
- Updates apps on schedule
- Reports installation status
```

#### **Scenario 5: iOS Device Management for Healthcare**

**Business Need**: Hospital needs to provide nurses with iPads for patient care while ensuring HIPAA compliance.

**Solution Walkthrough**:

1. Navigate to **Devices** > **iOS/iPadOS** > **iOS/iPadOS enrollment**
2. Review enrollment options:
   - **Device Enrollment Program (DEP)**: Automated enrollment
   - **User enrollment**: BYOD scenario
   - **Device enrollment**: Corporate-owned

3. Explore **Configuration profiles** for iOS:
   - **Restrictions**: Disable app store, camera in patient areas
   - **Email**: Configure Exchange ActiveSync
   - **Wi-Fi/VPN**: Healthcare network access
   - **Home screen layout**: Pin medical apps

4. Review **Compliance policies** for healthcare:
   - Device encryption required
   - Passcode complexity
   - Maximum OS version allowed
   - Jailbreak detection

**Real-World Example**:
```
Contoso Hospital deploys 200 iPads:
- Enrolled via Apple Business Manager
- Kiosk mode with only approved medical apps
- Camera disabled in patient areas
- Automatic screen lock after 2 minutes
- Remote wipe if device missing
- Compliance required for ePHI access
```

#### **Scenario 6: Multi-Platform Environment**

**Business Need**: Organization has Windows PCs, MacBooks, iPhones, and Android devices all needing management.

**Solution Walkthrough**:

1. Navigate to **Devices** > **All devices**
2. Review multi-platform capabilities:
   - Single console for all platforms
   - Consistent policy framework
   - Cross-platform reporting

3. Explore platform-specific sections:
   - **Windows**: GPO-like controls, Windows Update
   - **macOS**: FileVault, Gatekeeper settings
   - **iOS**: Supervised mode, DEP enrollment
   - **Android**: Work profile, fully managed

4. Review **Apps** > **All apps**:
   - Deploy platform-specific versions
   - Microsoft 365 across all platforms
   - Platform-appropriate LOB apps

**Real-World Example**:
```
Tailspin Toys device mix:
- 60% Windows 10/11 (Desktop workers)
- 20% MacBooks (Developers/Designers)
- 15% iPhones (Mobile workforce)
- 5% Android (Field technicians)

Single Intune tenant manages:
- All devices from one console
- Consistent security policies
- Cross-platform app deployment
- Unified reporting dashboard
```

#### **Scenario 7: Educational Institution**

**Business Need**: University needs to manage student and faculty devices across campus.

**Solution Walkthrough**:

1. Review **Tenant administration** > **Customization**:
   - Company Portal branding with university logo
   - Custom support information
   - Privacy statement links

2. Explore **Groups** strategy:
   - Faculty group (full access)
   - Student group (restricted)
   - IT Admin group (management rights)
   - Department-based groups

3. Review **Conditional Access** for education:
   - Students can only access resources on campus Wi-Fi
   - Faculty can access remotely with MFA
   - Guest access for visiting professors

**Real-World Example**:
```
Contoso University manages:
- 5,000 student devices (BYOD)
- 500 faculty devices (mixed ownership)
- 200 lab computers (shared devices)

Policies include:
- Students: Access to learning apps only
- Faculty: Full Microsoft 365 access
- Lab PCs: Shared device mode, kiosk
- All: Conditional access by location
```

#### Documentation Exercise

Create a document (in your notes) answering:

1. **For each scenario**, identify:
   - Main business challenge
   - Intune features used
   - Key benefits delivered

2. **Compare scenarios**:
   - Which scenarios use MAM vs MDM?
   - Which require Conditional Access?
   - Which use Autopilot?

3. **Your organization**:
   - Which scenario best matches your organization?
   - What additional requirements do you have?
   - Which features would provide the most value?

#### Verification

✅ You understand BYOD vs corporate device scenarios  
✅ You can identify appropriate Intune features for different needs  
✅ You recognize multi-platform management capabilities  
✅ You understand application deployment strategies  
✅ You can explain Conditional Access use cases

#### Lab Questions

1. What's the difference between MAM and MDM? When would you use each?
2. How does Autopilot simplify device deployment?
3. What Intune features help with HIPAA compliance?
4. How can Conditional Access improve security for remote workers?
5. What app deployment options are available in Intune?

---

## 📝 Knowledge Check Quiz

Test your understanding of Module 1 concepts:

### Question 1
What is the primary difference between traditional and modern device management?

A) Modern management requires more servers  
B) Modern management is cloud-based and platform-agnostic  
C) Traditional management supports more device types  
D) There is no significant difference

<details>
<summary>Click to reveal answer</summary>
**Answer: B** - Modern management is cloud-based, works across all platforms, and doesn't require on-premises infrastructure.
</details>

### Question 2
Which URL is used to access Microsoft Endpoint Manager admin center?

A) portal.azure.com  
B) admin.microsoft.com  
C) endpoint.microsoft.com  
D) intune.microsoft.com

<details>
<summary>Click to reveal answer</summary>
**Answer: C** - endpoint.microsoft.com is the current admin portal for Intune.
</details>

### Question 3
What is the primary benefit of MAM (Mobile Application Management)?

A) Full device control  
B) Protect company data without managing the entire device  
C) Faster device enrollment  
D) Lower licensing costs

<details>
<summary>Click to reveal answer</summary>
**Answer: B** - MAM protects company data within specific apps while maintaining user privacy on BYOD devices.
</details>

### Question 4
Which Intune feature enables zero-touch device deployment for Windows?

A) Configuration Manager  
B) Group Policy  
C) Windows Autopilot  
D) SCCM

<details>
<summary>Click to reveal answer</summary>
**Answer: C** - Windows Autopilot enables devices to be shipped directly to users and automatically configured.
</details>

### Question 5
What does MDM stand for in the context of Intune?

A) Microsoft Device Manager  
B) Mobile Data Management  
C) Modern Device Methodology  
D) Mobile Device Management

<details>
<summary>Click to reveal answer</summary>
**Answer: D** - MDM (Mobile Device Management) refers to managing the entire device.
</details>

---

## 📚 Additional Resources

### Microsoft Documentation
- [What is Microsoft Intune?](https://docs.microsoft.com/en-us/mem/intune/fundamentals/what-is-intune)
- [Intune planning guide](https://docs.microsoft.com/en-us/mem/intune/fundamentals/intune-planning-guide)
- [Supported devices and browsers](https://docs.microsoft.com/en-us/mem/intune/fundamentals/supported-devices-browsers)

### Video Resources
- Microsoft Mechanics: Intune Overview
- Microsoft Endpoint Manager Demo
- Modern Device Management Explained

### Community Resources
- [Microsoft Tech Community - Intune](https://techcommunity.microsoft.com/t5/microsoft-intune/bd-p/Microsoft-Intune)
- [r/Intune on Reddit](https://reddit.com/r/Intune)

---

## ✅ Module Completion Checklist

Before moving to Module 2, ensure you have:

- [ ] Accessed Microsoft Endpoint Manager admin center
- [ ] Explored all main menu sections (Devices, Apps, Users, Groups)
- [ ] Reviewed available device platforms
- [ ] Understood the difference between MAM and MDM
- [ ] Completed Lab 1.1: Admin Center Exploration
- [ ] Completed Lab 1.2: Use Case Scenarios
- [ ] Answered all knowledge check questions
- [ ] Can explain Intune's role in modern device management
- [ ] Identified relevant scenarios for your organization

---

## 🎯 What's Next?

In **Module 2 - Intune Fundamentals**, you'll learn:
- Detailed comparison of Intune vs Configuration Manager
- Intune architecture and components
- Licensing and subscription requirements
- Planning your Intune deployment

**Estimated Time for Module 2**: 2-3 hours

---

## 💡 Key Takeaways

✅ **Microsoft Intune** is a cloud-based device and application management service  
✅ **Modern management** enables management from anywhere without VPN  
✅ **MAM vs MDM**: MAM protects apps, MDM manages entire devices  
✅ **Multi-platform**: Intune manages Windows, iOS, Android, and macOS  
✅ **Integration**: Works seamlessly with Microsoft 365 and Azure AD  
✅ **Real-world value**: BYOD support, remote work enablement, security compliance

---

**Module Status**: ✅ Complete  
**Next Module**: [Module 2 - Intune Fundamentals](../02-intune-fundamentals/index.md)

---

*Last Updated: January 2026*  
*Course Version: 2.0*

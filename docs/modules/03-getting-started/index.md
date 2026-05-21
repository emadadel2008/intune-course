# Module 3 - Getting Started

## 📋 Module Overview

Welcome to Module 3! This is where your hands-on journey with Microsoft Intune truly begins. You'll set up your own trial environment, configure Azure AD, and enroll your first device. By the end of this module, you'll have a fully functional Intune environment ready for testing and learning.

## 🎯 Learning Objectives

By the end of this module, you will be able to:
- Create and configure a Microsoft 365 trial tenant
- Set up Azure Active Directory for device management
- Add and manage users and groups
- Configure Intune MDM authority
- Successfully enroll devices into Intune
- Understand the enrollment process for different platforms
- Verify successful device enrollment and management

## 📚 Module Content

### 3.1 Understanding Tenant Architecture

Before we begin setup, let's understand what we're building:

#### What is a Tenant?

A **tenant** is your organization's dedicated instance of Microsoft 365 and Azure services. Think of it as your own isolated environment in Microsoft's cloud.

```
┌─────────────────────────────────────────────────┐
│         Your Organization's Tenant              │
│      (yourcompany.onmicrosoft.com)              │
├─────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐              │
│  │  Azure AD   │  │  Microsoft  │              │
│  │  (Identity) │  │   Intune    │              │
│  └──────┬──────┘  └──────┬──────┘              │
│         │                │                      │
│         └────────┬───────┘                      │
│                  │                              │
│         ┌────────▼────────┐                     │
│         │   Users/Groups  │                     │
│         │     Devices     │                     │
│         │    Policies     │                     │
│         └─────────────────┘                     │
└─────────────────────────────────────────────────┘
```

**Key Components:**
- **Tenant Name**: yourcompany.onmicrosoft.com (permanent)
- **Azure AD**: Identity and access management
- **Intune Service**: Device and app management
- **Users & Groups**: People and collections
- **Devices**: Enrolled endpoints
- **Policies**: Configuration and compliance rules

### 3.2 Azure AD Integration Overview

#### Azure AD's Role in Intune

Azure Active Directory is the foundation of Intune. It provides:

1. **Identity Management**
   - User authentication
   - Device identity
   - Application registration

2. **Access Control**
   - Conditional Access policies
   - Multi-factor authentication
   - Risk-based access decisions

3. **Group Management**
   - User groups for policy targeting
   - Dynamic group membership
   - Security groups vs Distribution groups

4. **Device Registration**
   - Azure AD Join
   - Azure AD Registration
   - Hybrid Azure AD Join

#### Integration Flow

```
User Login
    ↓
Azure AD Authentication
    ↓
Device Check (Registered?)
    ↓
Conditional Access Evaluation
    ↓
Intune Compliance Check
    ↓
Access Granted/Denied
```

### 3.3 Device Enrollment Overview

#### Enrollment Methods by Platform

**Windows 10/11:**
- Azure AD Join + Auto-enrollment
- Azure AD Join via OOBE
- Group Policy enrollment
- Configuration Manager co-management
- Windows Autopilot

**iOS/iPadOS:**
- User enrollment (BYOD)
- Device enrollment (User Affinity)
- Automated Device Enrollment (DEP/ABM)
- Device enrollment without User Affinity

**Android:**
- Android Enterprise Work Profile (BYOD)
- Fully Managed (Corporate-owned)
- Corporate-owned work profile
- Dedicated devices (COSU/Kiosk)

**macOS:**
- User-approved enrollment
- Automated Device Enrollment (DEP)
- Direct enrollment

#### Enrollment Prerequisites

Before enrolling devices, you need:
- ✅ Active Microsoft 365/Intune subscription
- ✅ MDM Authority set to Intune
- ✅ Users created in Azure AD
- ✅ Users assigned Intune licenses
- ✅ Enrollment restrictions configured (optional)
- ✅ Company Portal app available

---

## 🔬 Hands-On Labs

### Lab 3.1: Create Microsoft 365 Trial Tenant

**Objective**: Set up your own Microsoft 365 E5 trial tenant for hands-on Intune learning.

**Prerequisites**:
- Valid email address (personal, not corporate)
- Phone number for verification
- Internet browser (Microsoft Edge or Chrome recommended)

**Estimated Time**: 20 minutes

#### Step-by-Step Instructions

**Step 1: Access the Trial Sign-up Page**

1. Open your web browser in **private/incognito mode** (important!)
2. Navigate to: `https://www.microsoft.com/en-us/microsoft-365/enterprise/office-365-e5`
3. Click **"Free trial"** or **"Try for free"**

**Why incognito?** To ensure you're not automatically signed into an existing Microsoft account.

**Step 2: Create Your Account**

1. Enter your email address (use a personal email)
   - ✅ Good: yourname@gmail.com, yourname@outlook.com
   - ❌ Bad: Existing work email
   
2. Click **"Set up account"**

3. Fill in the form:
   ```
   First Name: [Your First Name]
   Last Name: [Your Last Name]
   Business Phone: [Your Phone Number]
   Company Name: [Choose a name, e.g., "Contoso Labs"]
   Company Size: [Select 25-49 people]
   Country/Region: [Your country]
   ```

4. Click **"Next"**

**Step 3: Create Your Tenant Identity**

1. Create a unique username:
   ```
   Username: admin
   (You'll get: admin@[yourcompany].onmicrosoft.com)
   ```

2. Choose your tenant domain name:
   ```
   Example: contosolabs47
   Full domain: contosolabs47.onmicrosoft.com
   ```
   
   **Tips:**
   - Use something memorable
   - Add numbers if your first choice is taken
   - Write this down - you'll need it throughout the course!

3. Verify availability and click **"Next"**

**Step 4: Verify Your Identity**

1. Choose verification method:
   - **Text me** (SMS) - Recommended
   - **Call me** (Voice call)

2. Enter your phone number:
   ```
   Country Code: [Select your country]
   Phone Number: [Your mobile number]
   ```

3. Click **"Send verification code"**

4. Enter the 6-digit code you received

5. Click **"Verify"**

**Step 5: Create Admin Password**

1. Create a strong password:
   ```
   Requirements:
   - At least 8 characters
   - Uppercase letter (A-Z)
   - Lowercase letter (a-z)
   - Number (0-9)
   - Special character (!@#$%^&*)
   
   Example: IntuneAdmin2024!
   ```

2. **IMPORTANT**: Write down your credentials:
   ```
   Tenant Name: contosolabs47.onmicrosoft.com
   Username: admin@contosolabs47.onmicrosoft.com
   Password: [Your password]
   ```

3. Check **"I'd like to stay signed in"**

4. Click **"Next"**

**Step 6: Confirm Trial Setup**

1. Review the confirmation page showing:
   - Your trial includes 25 licenses
   - 30-day trial period
   - Microsoft 365 E5 features

2. Click **"Start using Microsoft 365 E5"**

**Step 7: Initial Portal Exploration**

1. You'll be taken to the Microsoft 365 admin center
   - URL: `https://admin.microsoft.com`

2. Skip or close any welcome tours

3. Take a moment to explore:
   - Left navigation menu
   - Home dashboard
   - Setup progress widget

**Step 8: Verify Intune Availability**

1. In the admin center, click **"Show all"** in the left menu

2. Scroll down to **"Admin centers"**

3. Verify you see:
   - ✅ Azure Active Directory
   - ✅ Endpoint Manager (Intune)
   - ✅ Security
   - ✅ Compliance

4. Click **"Endpoint Manager"**
   - URL: `https://endpoint.microsoft.com`
   - This is your Intune admin portal

5. Bookmark both portals:
   - Microsoft 365 Admin: `https://admin.microsoft.com`
   - Endpoint Manager: `https://endpoint.microsoft.com`

**Step 9: Document Your Environment**

Create a document with your tenant details:

```
═══════════════════════════════════════════
    MY INTUNE LAB ENVIRONMENT
═══════════════════════════════════════════

Tenant Information:
-------------------
Tenant Name: contosolabs47.onmicrosoft.com
Admin Username: admin@contosolabs47.onmicrosoft.com
Admin Password: [Your password]

Trial Details:
--------------
Start Date: [Today's date]
Expiration Date: [30 days from today]
License Type: Microsoft 365 E5 Trial
Number of Licenses: 25

Portal URLs:
------------
Microsoft 365 Admin: https://admin.microsoft.com
Endpoint Manager: https://endpoint.microsoft.com
Azure Portal: https://portal.azure.com
Azure AD: https://aad.portal.azure.com

Notes:
------
- Trial automatically converts to paid if credit card added
- Can extend trial or start new trial after expiration
- Data is retained for 90 days after expiration
```

#### Verification

Test your tenant setup:

1. **Sign out and sign back in**:
   - Go to `https://portal.office.com`
   - Use your admin credentials
   - Verify successful login

2. **Access all admin portals**:
   - ✅ Microsoft 365 Admin Center
   - ✅ Endpoint Manager (Intune)
   - ✅ Azure AD Portal
   - ✅ Azure Portal

3. **Check license assignment**:
   - Go to Microsoft 365 Admin Center
   - Navigate to **Users** > **Active users**
   - Click on your admin account
   - Verify **Microsoft 365 E5** license is assigned

#### Troubleshooting

**Issue: Domain name already taken**
- Solution: Add numbers or variations (e.g., contoso47, contoso-labs, contoso2024)

**Issue: SMS verification not received**
- Solution: Wait 2 minutes, try voice call option, or use different phone number

**Issue: Can't access Endpoint Manager**
- Solution: Wait 10 minutes for tenant provisioning, clear browser cache, try incognito mode

**Issue: License not showing**
- Solution: Sign out and back in, wait 5 minutes for sync, check billing section

#### Lab Questions

1. What is your tenant's onmicrosoft.com domain name?
2. How many trial licenses do you have?
3. When does your trial expire?
4. What license type is assigned to your admin account?
5. What are the URLs for the three main admin portals?

---

### Lab 3.2: Add Users and Groups in Azure AD

**Objective**: Create users and groups that will be used for device management and policy assignment.

**Prerequisites**:
- Completed Lab 3.1
- Access to Microsoft 365 admin center

**Estimated Time**: 30 minutes

#### Part 1: Create Individual Users

**Step 1: Access User Management**

1. Sign in to `https://admin.microsoft.com`

2. In the left menu, click **"Users"** > **"Active users"**

3. You should see your admin account listed

**Step 2: Create IT Administrator User**

1. Click **"Add a user"** at the top

2. Fill in basic information:
   ```
   First name: IT
   Last name: Admin
   Display name: IT Admin (auto-populated)
   Username: itadmin
   Domain: @[yourcompany].onmicrosoft.com
   ```

3. Uncheck **"Automatically create a password"**

4. Create a password:
   ```
   Password: ITadmin2024!
   ```

5. Uncheck **"Require this user to change their password..."**
   (For lab purposes only - in production, always require password change)

6. Click **"Next"**

**Step 3: Assign Licenses**

1. Select location: **[Your country]**

2. Check **"Microsoft 365 E5"**

3. Click **"Next"**

**Step 4: Assign Admin Roles**

1. Select **"Admin center access"**

2. Choose **"Intune Administrator"**
   - This gives full Intune/Endpoint Manager access
   - Without full Microsoft 365 admin rights

3. Click **"Next"**

**Step 5: Review and Create**

1. Review all settings

2. Click **"Finish adding"**

3. Copy the sign-in information (for your records)

4. Click **"Close"**

**Step 6: Create Additional Users**

Repeat the process to create these users:

**User 2: Sales Employee**
```
Name: Sales User
Username: salesuser
Password: Sales2024!
License: Microsoft 365 E5
Admin Role: None
```

**User 3: Marketing Employee**
```
Name: Marketing User
Username: marketinguser
Password: Marketing2024!
License: Microsoft 365 E5
Admin Role: None
```

**User 4: Finance Employee**
```
Name: Finance User
Username: financeuser
Password: Finance2024!
License: Microsoft 365 E5
Admin Role: None
```

**User 5: Contractor (BYOD)**
```
Name: Contract Worker
Username: contractor
Password: Contract2024!
License: Microsoft 365 E5
Admin Role: None
```

After creating all users, you should have:
- ✅ admin (Global Admin)
- ✅ itadmin (Intune Admin)
- ✅ salesuser
- ✅ marketinguser
- ✅ financeuser
- ✅ contractor

#### Part 2: Create Security Groups

**Step 1: Access Groups**

1. In the left menu, click **"Teams & groups"** > **"Active teams & groups"**

2. Click the **"Security groups"** tab

3. Click **"Add a security group"**

**Step 2: Create IT Administrators Group**

1. Fill in group details:
   ```
   Name: IT Administrators
   Description: IT staff with device management access
   ```

2. Click **"Next"**

3. **Owners**: Click "Add owners"
   - Search and select **"admin"**
   - Click **"Add"**

4. Click **"Next"**

5. **Members**: Click "Add members"
   - Search and select **"itadmin"**
   - Click **"Add"**

6. Click **"Next"**

7. Review and click **"Create group"**

8. Click **"Close"**

**Step 3: Create Department Groups**

Create these additional groups:

**Sales Department Group**
```
Name: Sales Department
Description: Sales team members
Members: salesuser
```

**Marketing Department Group**
```
Name: Marketing Department
Description: Marketing team members
Members: marketinguser
```

**Finance Department Group**
```
Name: Finance Department
Description: Finance team members
Members: financeuser
```

**BYOD Users Group**
```
Name: BYOD Users
Description: Users with personal devices
Members: contractor
```

**Step 4: Create All Devices Group (Dynamic)**

1. Navigate to **Azure AD Portal**: `https://aad.portal.azure.com`

2. Click **"Groups"** in the left menu

3. Click **"New group"**

4. Configure the dynamic group:
   ```
   Group type: Security
   Group name: All Managed Devices
   Group description: Automatically includes all Intune-managed devices
   Membership type: Dynamic Device
   ```

5. Click **"Add dynamic query"**

6. In the Rule builder:
   ```
   Property: managementType
   Operator: Equals
   Value: MDM
   ```

7. Click **"Save"**

8. Click **"Create"**

**Step 5: Create All Users Group (Dynamic)**

1. Click **"New group"** again

2. Configure:
   ```
   Group type: Security
   Group name: All Users
   Group description: Automatically includes all users with licenses
   Membership type: Dynamic User
   ```

3. Add dynamic query:
   ```
   Property: accountEnabled
   Operator: Equals
   Value: true
   ```

4. Click **"Save"** and **"Create"**

#### Part 3: Verify and Document Groups

**Step 1: Verify Group Memberships**

1. Go to **Azure AD** > **Groups**

2. Click on **"IT Administrators"**

3. Verify:
   - ✅ Owner: admin
   - ✅ Member: itadmin

4. Repeat for other groups

**Step 2: Create Groups Documentation**

Create this reference document:

```
═══════════════════════════════════════════
    AZURE AD GROUPS STRUCTURE
═══════════════════════════════════════════

ADMINISTRATIVE GROUPS
---------------------
Group: IT Administrators
Type: Security (Assigned)
Purpose: IT staff with device management access
Members: itadmin
Use Cases: Administrative policy assignments

DEPARTMENT GROUPS
-----------------
Group: Sales Department
Type: Security (Assigned)
Members: salesuser
Use Cases: Department-specific apps and policies

Group: Marketing Department
Type: Security (Assigned)
Members: marketinguser
Use Cases: Marketing tools and configurations

Group: Finance Department
Type: Security (Assigned)
Members: financeuser
Use Cases: Finance apps with enhanced security

SCENARIO-BASED GROUPS
---------------------
Group: BYOD Users
Type: Security (Assigned)
Members: contractor
Use Cases: BYOD-specific policies and MAM

DYNAMIC GROUPS
--------------
Group: All Managed Devices
Type: Security (Dynamic Device)
Query: managementType -eq "MDM"
Use Cases: Organization-wide device policies

Group: All Users
Type: Security (Dynamic User)
Query: accountEnabled -eq true
Use Cases: User-targeted policies
```

#### Part 4: Test User Accounts

**Step 1: Test User Sign-In**

1. Open a new **private/incognito browser window**

2. Navigate to `https://portal.office.com`

3. Sign in as: **salesuser@[yourtenant].onmicrosoft.com**

4. Use password: **Sales2024!**

5. Complete any first-time sign-in prompts

6. Verify access to:
   - ✅ Office web apps
   - ✅ OneDrive
   - ✅ Outlook

7. Sign out

8. Repeat for one other user account

#### Verification

✅ All 6 users created successfully  
✅ All users have licenses assigned  
✅ 5 security groups created  
✅ 2 dynamic groups created  
✅ Group memberships correct  
✅ Test user can sign in  
✅ Users appear in Azure AD

#### Lab Questions

1. How many types of groups did you create?
2. What's the difference between assigned and dynamic groups?
3. Why is the BYOD Users group useful?
4. Which user has Intune Administrator role?
5. What query did you use for the dynamic device group?

---

### Lab 3.3: Connect Azure AD with Intune

**Objective**: Configure the integration between Azure AD and Intune, set MDM authority, and enable automatic enrollment.

**Prerequisites**:
- Completed Lab 3.1 and 3.2
- Global Administrator access

**Estimated Time**: 20 minutes

#### Part 1: Set MDM Authority

**Step 1: Access Intune Portal**

1. Navigate to `https://endpoint.microsoft.com`

2. Sign in with your admin account

3. In the left menu, click **"Tenant administration"**

4. Click **"Tenant status"**

**Step 2: Verify MDM Authority**

1. Scroll down to **"MDM authority"** section

2. You should see:
   ```
   MDM authority: Microsoft Intune
   Status: Active
   ```

3. **Note**: For new tenants, this is automatically set

4. If not set, click **"Set MDM Authority to Intune"**

**Important**: MDM Authority cannot be changed once set. This decision is permanent for the tenant.

**Step 3: Review Tenant Details**

Document these settings:
```
Tenant Name: ________________
Tenant ID: ________________
MDM Authority: Microsoft Intune
Service Status: ________________
Location: ________________
```

#### Part 2: Configure Automatic Enrollment

**Step 1: Access Azure AD Portal**

1. Navigate to `https://portal.azure.com`

2. Click **"Azure Active Directory"** in the left menu

3. Scroll down and click **"Mobility (MDM and MAM)"**

**Step 2: Configure Microsoft Intune**

1. Click **"Microsoft Intune"** in the list

2. Configure **MDM User scope**:
   ```
   MDM user scope: Some
   ```

3. Click **"Groups"** under MDM user scope

4. Click **"Add groups"**

5. Select **"All Users"** (the dynamic group you created)

6. Click **"Select"**

**Why "Some" instead of "All"?**
- Provides control over which users can auto-enroll
- In production, you can gradually roll out
- For this lab, we're using the "All Users" group which includes everyone

**Step 3: Configure Privacy Statement**

1. Under **"Privacy and terms"**:
   ```
   Privacy statement URL: https://privacy.microsoft.com/privacystatement
   Terms and conditions: [Leave blank for now]
   ```

2. Click **"Save"**

#### Part 5: Configure Device Enrollment Restrictions

**Step 1: Access Enrollment Restrictions**

1. Go to **Devices** > **Enrollment restrictions**

2. You'll see two default policies:
   - All Users (Device Type Restrictions)
   - All Users (Device Limit Restrictions)

**Step 2: Configure Device Type Restrictions**

1. Click on **"All Users"** under Device Type Restrictions

2. Click **"Properties"**

3. Click **"Edit"** next to Platform settings

4. Configure which platforms to allow:
   ```
   Platform        Personal    Corporate-owned
   ────────        ────────    ───────────────
   Android         ✅ Allow    ✅ Allow
   iOS/iPadOS      ✅ Allow    ✅ Allow
   Windows         ✅ Allow    ✅ Allow
   macOS           ✅ Allow    ✅ Allow
   ```

5. Click **"Review + save"**

6. Click **"Save"**

**Step 3: Configure Device Limit**

1. Click on **"All Users"** under Device Limit Restriction

2. Click **"Properties"**

3. Click **"Edit"** next to Device limit

4. Set device limit:
   ```
   Device limit: 15
   ```
   (This allows each user to enroll up to 15 devices)

5. Click **"Review + save"**

6. Click **"Save"**

#### Part 6: Verify Integration

**Step 1: Check Azure AD Device Settings**

1. Go to Azure AD > **Devices** > **Device settings**

2. Verify:
   ```
   Users may join devices to Azure AD: All
   Users may register their devices: All
   Require Multi-Factor Auth to register: No (for lab)
   Maximum number of devices per user: 50
   ```

**Step 2: Test Integration**

1. Go to `https://endpoint.microsoft.com`

2. Click **"Devices"** > **"All devices"**

3. You should see an empty list (no devices enrolled yet)

4. This confirms Intune is ready to receive enrollments

#### Verification

✅ MDM authority set to Microsoft Intune  
✅ Automatic enrollment configured for All Users  
✅ MDM URLs verified  
✅ Company branding configured  
✅ Company Portal customized  
✅ Enrollment restrictions set  
✅ Azure AD device settings verified  
✅ Integration complete

#### Lab Questions

1. What is MDM authority and why can't it be changed?
2. Which group did you configure for automatic enrollment?
3. What is the device limit per user in your environment?
4. Why did we configure compangure MDM URLs**

Verify these URLs are set (should be automatic):

```
MDM Terms of use URL:
https://portal.manage.microsoft.com/TermsofUse.aspx

MDM Discovery URL:
https://enrollment.manage.microsoft.com/enrollmentserver/discovery.svc

MDM Compliance URL:
https://portal.manage.microsoft.com/?portalAction=Compliance
```

**Step 4: Configure MAM Settings** (Optional)

1. Scroll down to **"MAM User scope"**

2. For now, leave as: **None**
   (We'll configure MAM policies separately in Module 5)

3. Click **"Save"** at the top

**Step 5: Verify Configuration**

1. Refresh the page

2. Verify:
   - ✅ MDM user scope: Some (All Users group)
   - ✅ MDM URLs configured
   - ✅ Changes saved successfully

#### Part 3: Configure Company Branding (Optional but Recommended)

**Step 1: Access Company Branding**

1. In Azure AD, click **"Company branding"**

2. Click **"Configure"**

**Step 2: Customize Sign-in Experience**

1. Upload or create:
   ```
   Sign-in page background image: [Optional]
   Banner logo: [Optional - your company logo]
   Username hint: admin@yourtenant.onmicrosoft.com
   Sign-in page text: Welcome to Contoso Intune Lab
   ```

2. Click **"Save"**

3. Users will now see your branding at sign-in

#### Part 4: Configure Intune Customization

**Step 1: Access Intune Customization**

1. Go to `https://endpoint.microsoft.com`

2. Click **"Tenant administration"** > **"Customization"**

**Step 2: Configure Company Portal Branding**

1. Under **"Branding"** section, configure:
   ```
   Company name: Contoso Labs
   Theme color: #0078D4 (Microsoft Blue) or your choice
   Show in header: Company name
   Company logo: [Upload if available]
   ```

2. Under **"Support information"**:
   ```
   Contact name: IT Support
   Phone number: [Your number or  555-0100]
   Email address: support@yourtenant.onmicrosoft.com
   Website: https://yourtenant.sharepoint.com
   Additional information: Contact IT for device enrollment help
   ```

3. Click **"Save"**


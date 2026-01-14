# Module 4 - Device Enrollment & Management

## 📋 Module Overview

Module 4 takes you deep into device enrollment and management across all major platforms. You'll master different enrollment methods, create configuration profiles to standardize device settings, implement compliance policies, and manage Windows updates. This module provides the foundation for managing devices at scale in your organization.

## 🎯 Learning Objectives

By the end of this module, you will be able to:
- Enroll devices across all major platforms (Windows, iOS, Android, macOS)
- Choose appropriate enrollment methods for different scenarios
- Create and deploy configuration profiles
- Implement device compliance policies
- Configure Windows Update for Business
- Troubleshoot enrollment issues
- Manage enrolled devices effectively
- Apply different management strategies for BYOD vs corporate devices

## 📚 Module Content

### 4.1 Understanding Device Enrollment Methods

#### Enrollment Categories

**1. User-Driven Enrollment**
- User initiates enrollment
- Typically for BYOD scenarios
- User authenticates with Azure AD
- Device gets user affinity

**2. Admin-Driven Enrollment**
- IT pre-enrolls devices
- For corporate-owned devices
- Can be user-less (kiosks, shared devices)
- Automated through Autopilot or DEP

**3. Bulk Enrollment**
- Enroll multiple devices simultaneously
- Using provisioning packages
- For large-scale deployments

#### Enrollment Methods by Platform

```
┌──────────────────────────────────────────────────────┐
│                  ENROLLMENT MATRIX                    │
├──────────────────────────────────────────────────────┤
│                                                       │
│  Windows 10/11                                        │
│  ├─ Azure AD Join + Auto-enroll (Recommended)        │
│  ├─ Azure AD Join during OOBE                        │
│  ├─ Group Policy enrollment                          │
│  ├─ Windows Autopilot (Zero-touch)                   │
│  ├─ Bulk enrollment (Provisioning packages)          │
│  └─ Co-management (with ConfigMgr)                   │
│                                                       │
│  iOS/iPadOS                                           │
│  ├─ User Enrollment (BYOD)                           │
│  ├─ Device Enrollment (Corporate, user affinity)     │
│  ├─ Automated Device Enrollment/DEP (ABM/ASM)        │
│  └─ Device Enrollment without user affinity          │
│                                                       │
│  Android                                              │
│  ├─ Work Profile (BYOD - Recommended)                │
│  ├─ Fully Managed (Corporate-owned)                  │
│  ├─ Corporate-owned Work Profile                     │
│  ├─ Dedicated Devices (COSU/Kiosk)                   │
│  └─ Device Administrator (Deprecated)                │
│                                                       │
│  macOS                                                │
│  ├─ User-approved MDM enrollment                     │
│  ├─ Automated Device Enrollment (DEP)                │
│  └─ Direct enrollment (user-less devices)            │
│                                                       │
└──────────────────────────────────────────────────────┘
```

### 4.2 Configuration Profiles Deep Dive

#### What are Configuration Profiles?

Configuration profiles are XML-based files that define device settings and restrictions. They allow you to:
- Configure device features
- Set up Wi-Fi, VPN, email
- Control device capabilities
- Enforce security settings
- Customize user experience

#### Profile Types

**Device Configuration Profiles:**
- Templates (Pre-configured settings)
- Settings Catalog (Granular controls)
- Administrative Templates (GPO-like)
- Custom Profiles (OMA-URI, XML)

**Profile Categories:**

```
1. Device Features
   - Home screen layout
   - Notifications
   - Lock screen
   - Display settings

2. Device Restrictions
   - App restrictions
   - Hardware restrictions (camera, Bluetooth)
   - Password requirements
   - Cloud and storage restrictions

3. Connectivity
   - Wi-Fi
   - VPN
   - Certificates
   - Email

4. Security
   - Encryption
   - Firewall
   - Antivirus
   - BitLocker

5. Platform-Specific
   - Windows: ADMX templates
   - iOS: Restrictions payload
   - Android: Enterprise settings
   - macOS: Preference files
```

### 4.3 Compliance Policies Overview

#### Compliance vs Configuration

**Configuration Profiles:**
- **What they do**: Apply settings to devices
- **Purpose**: Standardize configurations
- **Action**: Configure devices
- **Example**: Set Wi-Fi, install certificates

**Compliance Policies:**
- **What they do**: Evaluate device state
- **Purpose**: Enforce minimum requirements
- **Action**: Report compliance status
- **Example**: Require encryption, OS version

#### Compliance Policy Flow

```
Device Enrolled
     ↓
Compliance Policy Assigned
     ↓
Device Evaluated (Every 8 hours)
     ↓
├─ Compliant → Access Granted
└─ Non-Compliant → 
     ↓
   Grace Period (if configured)
     ↓
   Actions Triggered:
   - Send notification
   - Mark non-compliant
   - Block access (with Conditional Access)
   - Retire device (after X days)
```

#### Common Compliance Settings

**All Platforms:**
- Device encryption required
- Minimum OS version
- Maximum OS version
- Password/PIN requirements
- Jailbroken/Rooted detection

**Windows Specific:**
- BitLocker required
- Secure Boot enabled
- Code integrity enabled
- Firewall enabled
- Antivirus enabled and up-to-date

**Mobile Specific:**
- Device threat level
- Work profile security
- System integrity check

### 4.4 Windows Update for Business

#### Update Rings

Update rings control when devices receive Windows updates:

```
┌─────────────────────────────────────────┐
│          UPDATE RING STRATEGY            │
├─────────────────────────────────────────┤
│                                          │
│  Ring 1: IT Testing (Pilot)             │
│  ├─ Defer feature updates: 0 days       │
│  ├─ Defer quality updates: 0 days       │
│  └─ Group: IT Administrators            │
│                                          │
│  Ring 2: Early Adopters                 │
│  ├─ Defer feature updates: 30 days      │
│  ├─ Defer quality updates: 7 days       │
│  └─ Group: Power Users                  │
│                                          │
│  Ring 3: General Deployment             │
│  ├─ Defer feature updates: 60 days      │
│  ├─ Defer quality updates: 14 days      │
│  └─ Group: All Users                    │
│                                          │
│  Ring 4: Critical Systems               │
│  ├─ Defer feature updates: 180 days     │
│  ├─ Defer quality updates: 21 days      │
│  └─ Group: Production Servers           │
│                                          │
└─────────────────────────────────────────┘
```

**Update Types:**

1. **Feature Updates**
   - Major Windows versions (e.g., 22H2, 23H2)
   - Released twice per year
   - Can defer up to 365 days

2. **Quality Updates**
   - Monthly security and bug fixes
   - "Patch Tuesday" releases
   - Can defer up to 30 days

3. **Driver Updates**
   - Hardware drivers
   - Optional control

**Update Settings:**
- Installation deadlines
- Restart notifications
- Active hours (prevent restarts)
- Update installation time
- Automatic restart with logged-on users

---

## 🔬 Hands-On Labs

### Lab 4.1: Enroll Windows 10/11 Device (Advanced Methods)

**Objective**: Master multiple Windows enrollment methods including Autopilot preparation and bulk enrollment.

**Prerequisites**:
- Windows 10 (1903+) or Windows 11 device
- Not domain-joined
- Administrator access
- Completed Module 3

**Estimated Time**: 45 minutes

#### Method 1: Azure AD Join with Auto-enrollment (Standard)

**Step 1: Verify Prerequisites**

1. Check Windows version:
   - Press `Win + R`
   - Type `winver` and press Enter
   - Verify: Windows 10 version 1903+ or Windows 11

2. Check edition:
   - Open Settings > System > About
   - Verify: Pro, Enterprise, or Education
   - Note: Windows Home doesn't support Azure AD Join

**Step 2: Remove Existing Connections**

1. Go to Settings > Accounts > Access work or school

2. If any accounts are listed:
   - Click on each account
   - Click "Disconnect"
   - Confirm removal
   - Restart if prompted

**Step 3: Join Azure AD**

1. In Settings > Accounts > Access work or school

2. Click "Connect"

3. Click "Join this device to Azure Active Directory"

4. Sign in with test user:
   ```
   Email: salesuser@yourtenant.onmicrosoft.com
   Password: Sales2024!
   ```

5. On "Make sure this is your organization":
   - Verify organization name
   - Click "Join"

6. Wait for enrollment (1-3 minutes)

7. On "You're all set!":
   - Review settings applied
   - Click "Done"

**Step 4: Verify Enrollment**

1. Open Settings > Accounts > Access work or school

2. Verify you see:
   ```
   Connected to [Your Tenant] Azure AD
   Managed by [Your Tenant]
   ```

3. Click "Info" button

4. Verify:
   - Device sync status
   - Areas managed by organization
   - Last sync time

5. Click "Sync" to force immediate sync

**Step 5: Verify in Intune Portal**

1. Go to `https://endpoint.microsoft.com`

2. Navigate to Devices > Windows > Windows devices

3. Find your device and verify:
   ```
   Device name: [Your device name]
   Managed by: Intune
   Ownership: Corporate
   Compliance: Evaluating or Compliant
   Enrollment type: Azure AD joined
   Primary user: salesuser
   OS version: [Windows version]
   Last check-in: [Recent timestamp]
   ```

#### Method 2: Bulk Enrollment with Provisioning Package

**Step 1: Install Windows Configuration Designer**

1. Open Microsoft Store

2. Search for "Windows Configuration Designer"

3. Install the app (by Microsoft Corporation)

4. Launch Windows Configuration Designer

**Step 2: Create Provisioning Package**

1. In WCD, click "Provision desktop devices"

2. Configure project:
   ```
   Name: IntuneEnrollment
   Description: Bulk enrollment for Intune
   ```

3. Click "Finish"

**Step 3: Configure Package Settings**

1. **Set up device**: Configure basic settings
   ```
   Device name: INTUNE-%RAND:5%
   (This creates random device names)
   ```

2. **Set up network**: Skip (we'll use Wi-Fi profile later)

3. **Account Management**:
   - Select "Enroll in Azure AD"
   - Click "Get Bulk Token"
   - Sign in with admin account
   - Token will be embedded in package

4. **Add applications**: Skip for now

5. **Add certificates**: Skip for now

6. **Finish**: Review settings

**Step 4: Build the Package**

1. Click "Create"

2. Choose export location:
   ```
   Save to: Desktop\IntuneEnrollment
   ```

3. Check "Build" and uncheck "Encrypt"

4. Click "Build"

5. Wait for package creation (30 seconds)

6. Copy the `.ppkg` file to USB drive

**Step 5: Apply Provisioning Package**

1. On a fresh Windows device (or reset device)

2. During OOBE (Out of Box Experience):
   - Press Windows key 5 times
   - Click "Set up for an organization"
   - Click "Next"

3. Select "Set up for work or school"

4. Insert USB drive with .ppkg file

5. Windows will detect the package

6. Click "Yes, add it"

7. Enter admin password if prompted

8. Device will automatically:
   - Join Azure AD
   - Enroll in Intune
   - Apply initial policies

**Step 6: Document Your Enrollment**

Create this document:
```
WINDOWS ENROLLMENT METHODS TESTED
──────────────────────────────────

Method 1: Azure AD Join
Device: [Name]
User: salesuser
Time to enroll: [X] minutes
Status: ✅ Success
Notes: Standard method, works well

Method 2: Bulk Enrollment
Devices enrolled: [Number]
Package created: [Date]
Status: ✅ Success
Notes: Good for multiple devices

Challenges encountered:
- [List any issues]

Solutions applied:
- [How you resolved them]
```

#### Method 3: Prepare for Windows Autopilot (Setup Only)

**Note**: Full Autopilot requires hardware hash registration. This section prepares you for the process.

**Step 1: Understand Autopilot Requirements**

Autopilot enables:
- Zero-touch deployment
- Devices ship directly to users
- Automatic configuration on first boot

Requirements:
- Windows 10 1903+ or Windows 11
- Internet connectivity
- Device hardware hash in Intune

**Step 2: Collect Hardware Hash**

1. On the Windows device, open PowerShell as Admin

2. Run this command to install required script:
   ```powershell
   Install-Script -Name Get-WindowsAutopilotInfo
   ```

3. If prompted about NuGet, type "Y"

4. Collect hardware hash:
   ```powershell
   Get-WindowsAutopilotInfo -OutputFile C:\Temp\AutopilotHash.csv
   ```

5. The CSV file contains your device's hardware hash

**Step 3: Import to Autopilot (Portal)**

1. Go to `https://endpoint.microsoft.com`

2. Navigate to Devices > Windows > Windows enrollment

3. Click "Devices" under Windows Autopilot

4. Click "Import"

5. Select your CSV file

6. Click "Import"

7. Wait for processing (can take 15 minutes)

**Step 4: Create Autopilot Profile (Future Use)**

1. In Windows Autopilot, click "Deployment Profiles"

2. Click "Create profile" > "Windows PC"

3. Configure:
   ```
   Name: Standard Autopilot Profile
   Convert all targeted devices to Autopilot: Yes
   Deployment mode: User-driven
   Join to Azure AD as: Azure AD joined
   ```

4. OOBE settings:
   ```
   Language: User selection
   Keyboard: User selection
   EULA: Hide
   Privacy settings: Hide
   Hide change account options: Yes
   ```

5. Assign to: All devices (or specific group)

6. Click "Create"

**Note**: To fully test Autopilot, you'd need to reset the device and go through OOBE again.

#### Verification

✅ Successfully enrolled Windows device via Azure AD Join  
✅ Created and applied provisioning package  
✅ Collected device hardware hash  
✅ Device appears in Intune portal  
✅ Device is managed and receiving policies  
✅ Understanding of Autopilot process

#### Troubleshooting

**Issue: "Can't join this device to Azure AD"**
```
Solutions:
1. Verify Windows edition (must be Pro/Enterprise/Education)
2. Ensure internet connectivity
3. Remove from on-premises domain first
4. Check user has Intune license
5. Verify auto-enrollment is configured
```

**Issue: Provisioning package fails**
```
Solutions:
1. Regenerate bulk token (expires after 180 days)
2. Rebuild package
3. Verify package is not encrypted
4. Check device has internet connection
5. Try applying during OOBE instead of post-setup
```

#### Lab Questions

1. What's the difference between Azure AD Join and Azure AD Register?
2. When would you use a provisioning package vs manual enrollment?
3. What information does the hardware hash contain?
4. How long is an Azure AD bulk token valid?
5. What Windows editions support Azure AD Join?

---

### Lab 4.2: Enroll Android Device (Work Profile & Fully Managed)

**Objective**: Master both BYOD (Work Profile) and corporate-owned (Fully Managed) Android enrollment methods.

**Prerequisites**:
- Android device (8.0+)
- Factory reset capability (for fully managed)
- Test user credentials
- Google Play Store access

**Estimated Time**: 60 minutes (30 min per method)

#### Part 1: Work Profile Enrollment (BYOD Scenario)

**What is Android Enterprise Work Profile?**
- Creates isolated work container on personal device
- Separates work and personal data
- Work apps have briefcase badge
- Personal data remains private
- IT can't access personal apps or data

**Step 1: Prepare Device**

1. Ensure device is running Android 8.0 or later:
   - Settings > About phone > Android version

2. Verify Google Play Store is available

3. Have user credentials ready:
   ```
   User: contractor@yourtenant.onmicrosoft.com
   Password: Contract2024!
   ```

**Step 2: Initiate Enrollment**

**Method A: Using Company Portal App**

1. Open Google Play Store

2. Search for "Intune Company Portal"

3. Install "Intune Company Portal" by Microsoft Corporation

4. Open Company Portal

5. Tap "Sign in"

6. Enter contractor credentials

7. Tap "Sign in"

**Method B: Using QR Code (Recommended for Scale)**

1. Generate QR code first:
   - Go to `https://endpoint.microsoft.com`
   - Devices > Android > Android enrollment
   - Enrollment options > Personally-owned devices with work profile
   - Click "Create enrollment profile"
   - Generate QR code
   - Save QR code image

2. On Android device during setup:
   - Tap screen 6 times on "Welcome" screen
   - Device prompts for QR code
   - Scan the QR code
   - Follow enrollment wizard

**Step 3: Set Up Work Profile**

1. Company Portal will prompt: "Set up your work profile"

2. Tap "Continue"

3. Grant permissions when requested:
   - Location (optional)
   - Contacts (for work profile)
   - Phone

4. Create work profile security:
   ```
   Create work profile password/PIN
   (Separate from personal device lock)
   ```

5. Tap "Continue"

6. Wait for work profile creation (1-2 minutes)

**Step 4: Complete Company Portal Setup**

1. Review what your organization can/cannot see:
   ```
   Can see:
   - Work apps installed
   - Work profile data
   - Network usage of work apps
   
   Cannot see:
   - Personal apps
   - Personal browsing history
   - Personal messages/calls
   - Location (unless work app uses it)
   ```

2. Tap "Continue"

3. Review compliance requirements

4. Tap "Resolve" for any non-compliant items:
   - Screen lock (if required)
   - Encryption (usually automatic)

5. Tap "Check device settings"

6. When compliant: Tap "Continue"

7. Setup complete: Tap "Done"

**Step 5: Explore Work Profile**

1. Open app drawer

2. Notice two tabs/sections:
   ```
   Personal:
   - Your personal apps
   - No briefcase badge
   
   Work:
   - Company Portal
   - Managed Google Play Store
   - Apps have briefcase badge 🔒
   ```

3. Open "Managed Google Play Store":
   - Only approved apps visible
   - Automatically silently installed if required

4. Try switching between profiles:
   - Swipe down notification shade
   - Tap work profile icon to pause/resume

**Step 6: Verify Data Separation**

Test data isolation:

1. Create a contact in personal Contacts app

2. Open Work Contacts app (if installed)

3. Verify: Personal contact NOT visible in work app

4. Install a work app (like Microsoft Teams):
   - From Managed Play Store
   - Note briefcase badge on icon

5. Try sharing between personal and work:
   - Take a photo
   - Try sharing to work Teams
   - May be blocked by policy (we'll configure this later)

**Step 7: Verify in Intune Portal**

1. Go to `https://endpoint.microsoft.com`

2. Navigate to Devices > Android > Android devices

3. Find your device:
   ```
   Device name: [Manufacturer Model]
   Enrolled user: contractor
   Ownership: Personal
   Management mode: Android Enterprise - Work profile
   Compliance: Compliant
   OS version: Android [version]
   Android security patch: [date]
   ```

4. Click on device name

5. Review tabs:
   - Overview: Basic info
   - Hardware: Detailed specs
   - Discovered apps: Only work profile apps visible
   - Device compliance: Compliance status

**Step 8: Test Work Profile Features**

1. On device, swipe down notifications

2. Tap work profile icon

3. Options:
   ```
   - Pause work profile (apps stopped, notifications hidden)
   - Turn off work profile
   - Settings
   ```

4. Pause work profile:
   - Work apps grayed out
   - No work notifications
   - IT cannot track device

5. Resume work profile:
   - Apps available again
   - Notifications resume

#### Part 2: Fully Managed Enrollment (Corporate-Owned)

**What is Fully Managed?**
- Entire device managed by IT
- No personal profile separation
- Corporate-owned device only
- Full device control
- Can enforce stronger restrictions

**⚠️ WARNING**: This will factory reset your device!

**Step 1: Prepare for Fully Managed Enrollment**

1. **Backup personal data** (photos, contacts, etc.)

2. In Intune portal, create enrollment token:
   - Go to Devices > Android > Android enrollment
   - Click "Corporate-owned, fully managed user devices"
   - Click "Create token"
   - Give it a name: "Fully Managed Token"
   - Copy the token string
   - Note expiration date

**Step 2: Factory Reset Device**

1. On Android device: Settings > System > Reset options

2. Tap "Erase all data (factory reset)"

3. Confirm reset

4. Device will reboot to setup wizard

**Step 3: Enroll During Setup**

1. On "Welcome" screen:
   - Connect to Wi-Fi
   - Tap screen 6 times (activates QR code scanner)

2. **Method A**: Scan QR code
   - Generate QR code in Intune portal with token
   - Scan with device camera
   - Follow prompts

3. **Method B**: Manual token entry
   - Tap "Set up for work"
   - When prompted, enter: `afw#intune`
   - Install Android Device Policy
   - Sign in with admin or user credentials
   - Paste enrollment token

**Step 4: Complete Setup**

1. Sign in with user:
   ```
   Email: marketinguser@yourtenant.onmicrosoft.com
   Password: Marketing2024!
   ```

2. Accept terms and conditions

3. Set device lock screen:
   - PIN, Pattern, or Password
   - Required by policy

4. Grant permissions:
   - Location
   - Phone
   - Storage

5. Wait for enrollment and app installation (5-10 minutes)

**Step 5: Verify Fully Managed Mode**

1. Notice differences from work profile:
   ```
   - No "personal" vs "work" app separation
   - All apps are managed
   - Company Portal shows "Corporate-owned"
   - Stronger restrictions possible
   ```

2. Open Settings:
   - Some settings may be grayed out
   - Managed by organization watermark

3. Try installing app from Play Store:
   - May be blocked by policy
   - Only approved apps installable

**Step 6: Verify in Intune Portal**

1. Go to Devices > Android > Android devices

2. Find the device:
   ```
   Ownership: Corporate
   Management mode: Android Enterprise - Fully managed
   Enrolled user: marketinguser
   Serial number: [visible]
   IMEI: [visible]
   ```

3. Available device actions:
   ```
   - Sync
   - Retire
   - Wipe
   - Remote lock
   - Reset passcode
   - Reboot (requires Android 11+)
   ```

#### Part 3: Compare Enrollment Methods

Create this comparison document:

```
ANDROID ENROLLMENT COMPARISON
─────────────────────────────────────────────

WORK PROFILE (BYOD)
───────────────────
Device: [Your device]
User: contractor
Ownership: Personal

Pros:
✅ User privacy maintained
✅ Work/personal separation
✅ Less intrusive management
✅ User can pause work profile
✅ Personal data not visible to IT

Cons:
❌ Limited device control
❌ Cannot disable personal apps
❌ Some features unavailable
❌ Users can unenroll easily

Best for:
- BYOD scenarios
- User-owned devices
- Privacy-conscious users


FULLY MANAGED (Corporate)
─────────────────────────
Device: [Your device]
User: marketinguser
Ownership: Corporate

Pros:
✅ Full device control
✅ Enforce stricter policies
✅ Can block app installation
✅ Hardware inventory visible
✅ Complete device wipe

Cons:
❌ No user privacy
❌ Cannot be used personally
❌ More intrusive
❌ Requires device reset

Best for:
- Corporate-owned devices
- Shared devices
- High-security environments
- Dedicated use cases (kiosks)
```

#### Part 4: Additional Enrollment Method - Corporate-Owned Work Profile

**What is it?**
- Hybrid approach
- Work profile on corporate device
- Better balance of control and privacy
- Available Android 8.0+

**Quick Setup** (informational - optional to test):

1. During factory reset setup:
   - Choose corporate-owned enrollment
   - Select "Corporate-owned work profile"
   - Complete enrollment

2. Result:
   - Work profile created
   - Corporate ownership
   - IT can enforce more policies than personal work profile
   - User still has some privacy

#### Verification

✅ Enrolled device with Work Profile  
✅ Enrolled device as Fully Managed  
✅ Understand data separation in work profile  
✅ Can pause/resume work profile  
✅ Devices appear correctly in Intune  
✅ Understand enrollment method differences  
✅ Know which method for which scenario

#### Troubleshooting

**Issue: "This device can't be enrolled"**
```
Solutions:
1. Verify Android 8.0 or later
2. Check Google Play Services installed and updated
3. Ensure enrollment restrictions allow Android
4. Verify user has Intune license
5. Check enrollment token hasn't expired (fully managed)
```

**Issue: Work profile won't create**
```
Solutions:
1. Clear Company Portal app data
2. Uninstall and reinstall Company Portal
3. Ensure device not already managed
4. Check for existing work profile (remove it)
5. Try QR code method instead
```

**Issue: Can't factory reset for fully managed**
```
Solutions:
1. Use a test device or emulator
2. Backup all data first
3. Use Android Studio emulator for testing
4. Create documentation instead of hands-on
```

#### Lab Questions

1. What's the key difference between Work Profile and Fully Managed?
2. Can users unenroll from a Fully Managed device?
3. What can IT see on a Work Profile device?
4. When would you use Corporate-owned Work Profile?
5. How do you generate a QR code for enrollment?
6. What is the `afw#intune` command used for?

---

### Lab 4.3: Enroll iOS/iPadOS Device

**Objective**: Master iOS/iPadOS enrollment methods including User Enrollment and Device Enrollment.

**Prerequisites**:
- iPhone or iPad (iOS/iPadOS 13+)
- Not supervised or managed
- Test user credentials
- Apple ID (optional, for App Store)

**Estimated Time**: 40 minutes

#### Part 1: User Enrollment (BYOD - Recommended for Personal Devices)

**What is User Enrollment?**
- Lightweight management for BYOD
- Uses Managed Apple ID
- Separates work and personal data
- Less invasive than Device Enrollment
- Available iOS 13+

**Step 1: Configure User Enrollment in Intune**

1. Go to `https://endpoint.microsoft.com`

2. Navigate to Devices > iOS/iPadOS > iOS/iPadOS enrollment

3. Click "Enrollment types"

4. Click "Create profile"

5. Select "iOS/iPadOS"

6. Configure:
   ```
   Name: iOS User Enrollment
   Description: BYOD enrollment for iOS devices
   Enrollment type: User enrollment
   Platform: iOS/iPadOS
   ```

7. User affinity: "Enroll with user affinity"

8. Assign to "BYOD Users" group

9. Click "Create"

**Step 2: Enroll iOS Device**

1. On iOS device, open **Settings**

2. Tap on your name at top

3. Scroll down and tap "Sign in to your iPhone" (if not signed in)
   - Or use existing Apple ID

4. Return to Settings > General

5. Tap "VPN & Device Management"

6. Tap "Add account"

7. Select "Sign in to work or school"

8. Enter credentials:
   ```
   Email: contractor@yourtenant.onmicrosoft.com
   Password: Contract2024!
   ```

9. Tap "Sign in"

10. Review management profile

11. Tap "Install"

12. Enter device passcode

13. Tap "Install" on warning

14. Tap "Trust"

15. Tap "Done"

**Step 3: Install Company Portal**

1. Open App Store

2. Search "Intune Company Portal"

3. Install app by Microsoft Corporation

4. Open Company Portal

5. Sign in with same credentials

6. Tap "Begin" on device setup

7. Review privacy information

8. Tap "Continue"

9. Review compliance requirements

10. Tap "Check compliance"

11. Resolve any issues

12. Tap "Done"

**Step 4: Verify User Enrollment**

1. In Settings > General > VPN & Device Management

2. Tap on management profile

3. Verify:
   ```
   Type: Device Enrollment
   Status: Verified
   Organization: [Your tenant]
   Installed: [Today's date]
   ```

4. Review managed areas:
   - Configuration profiles installed
   - Restrictions applied
   - Apps managed
   - Certificates

**Step 4: Verify in Intune Portal**

1. Go to `https://endpoint.microsoft.com`

2. Navigate to Devices > iOS/iPadOS > iOS/iPadOS devices

3. Find your device(s):
   ```
   BYOD Device (User Enrollment):
   - Device name: iPhone of Contractor
   - User: contractor
   - Ownership: Personal
   - Enrollment: User enrollment
   - Supervised: No
   
   Corporate Device (Device Enrollment):
   - Device name: iPhone of Sales User  
   - User: salesuser
   - Ownership: Corporate
   - Enrollment: Device enrollment
   - Supervised: No
   ```

4. Click on each device to compare:
   - Hardware details
   - Installed apps (work apps only for user enrollment)
   - Available actions

#### Part 3: Understanding Automated Device Enrollment (DEP/ABM)

**Note**: Requires Apple Business Manager account. This section is informational.

**What is Automated Device Enrollment?**
- Zero-touch deployment
- Devices enrolled during activation
- Purchased through Apple Business Manager or Apple School Manager
- Can make devices supervised
- Best for large deployments

**Prerequisites**:
- Apple Business Manager (ABM) account
- Devices purchased through Volume Purchase Program
- MDM server token uploaded to ABM

**Setup Process** (Overview):

1. **In Apple Business Manager**:
   - Assign devices to MDM server (Intune)
   - Create enrollment profiles

2. **In Intune**:
   ```
   Devices > iOS > iOS enrollment > Enrollment program tokens
   - Upload token from ABM
   - Sync devices
   - Create enrollment profile
   - Assign to devices
   ```

3. **On Device**:
   - User unboxes device
   - Connects to Wi-Fi
   - Setup Assistant automatically enrolls
   - Apps and policies apply
   - Ready to use

**Benefits**:
- No manual enrollment needed
- Devices can't skip enrollment
- Supervise devices remotely
- User can't remove management
- Lockdown device capabilities

#### Part 4: Compare iOS Enrollment Methods

Create comparison document:

```
iOS/iPadOS ENROLLMENT COMPARISON
────────────────────────────────────────

USER ENROLLMENT (BYOD)
──────────────────────
Device tested: iPhone of Contractor
User: contractor
Ownership: Personal

Management Scope:
✅ Work apps and data only
✅ Work email/calendar
✅ Work certificates
✅ Some device restrictions
❌ Cannot access personal data
❌ Limited device control

Data Separation:
- Work data encrypted separately
- Personal apps not visible to IT
- Managed Apple ID for work

User Control:
- Can remove enrollment
- Can delete work apps
- Privacy protected

Best for: Personal devices, BYOD


DEVICE ENROLLMENT (Corporate)
─────────────────────────────
Device tested: iPhone of Sales User
User: salesuser
Ownership: Corporate

Management Scope:
✅ Entire device managed
✅ All apps visible to IT
✅ Full configuration control
✅ Stronger restrictions
✅ Complete device inventory

Data Visibility:
- All installed apps visible
- Device location can be tracked
- Full hardware inventory

Device Control:
- Full device wipe available
- Lost mode can be enabled
- User cannot easily remove enrollment

Best for: Corporate-owned devices


AUTOMATED DEVICE ENROLLMENT (Enterprise)
─────────────────────────────────────────
Ownership: Corporate
Requires: Apple Business Manager

Setup:
- Zero-touch deployment
- Enrollment during activation
- Cannot skip enrollment
- Devices supervised

Control:
✅ Highest level of management
✅ Mandatory enrollment
✅ Cannot remove management
✅ Advanced restrictions available
✅ Supervised mode features

Best for:
- Large deployments
- Maximum control needed
- Education institutions
- Enterprise deployments
```

#### Part 5: Test iOS Device Actions

**Step 1: Available Actions for User Enrollment**

In Intune portal, select user-enrolled device:

Available actions:
```
- Sync
- Retire (removes work data only)
- Wipe (factory reset - requires supervised)
- Remote lock (supervised only)
```

Test Sync:
1. Click "Sync"
2. Wait for completion
3. On device, check Settings for sync time

**Step 2: Available Actions for Device Enrollment**

Select device-enrolled device:

Available actions:
```
- Sync
- Retire
- Wipe
- Remote lock
- Restart (supervised only)
- Lost mode (supervised only)
```

Test Retire:
1. Note: This will remove management!
2. Click "Retire"
3. Confirm action
4. On device, management profile removed
5. Work apps deleted
6. Personal data untouched

**Step 3: Re-enroll After Testing**

If you retired device:
1. Follow enrollment steps again
2. Device can be re-enrolled
3. Policies reapplied

#### Verification

✅ Enrolled iOS device with User Enrollment  
✅ Enrolled iOS device with Device Enrollment  
✅ Understand data separation in User Enrollment  
✅ Both devices appear in Intune portal  
✅ Tested device sync  
✅ Know enrollment method differences  
✅ Understand when to use each method  
✅ Understand Automated Device Enrollment concept

#### Troubleshooting

**Issue: "Cannot install profile"**
```
Solutions:
1. Ensure iOS 13 or later
2. Remove any existing MDM profiles
3. Restart device
4. Check internet connectivity
5. Verify user has Intune license
6. Try enrolling via Settings instead of Company Portal
```

**Issue: "Profile installation failed"**
```
Solutions:
1. Check enrollment restrictions in Intune
2. Verify iOS platform is allowed
3. Ensure user in correct group
4. Check for expired enrollment tokens
5. Review Intune enrollment logs
```

**Issue: Company Portal shows "Not compliant"**
```
Solutions:
1. Review compliance requirements
2. Update iOS to required version
3. Set device passcode if required
4. Wait for compliance evaluation (can take 8 hours)
5. Manually sync device
```

#### Lab Questions

1. What's the key difference between User and Device enrollment?
2. What can IT see with User Enrollment?
3. Can users remove Device Enrollment easily?
4. What is Automated Device Enrollment (DEP)?
5. What does "supervised" mean for iOS devices?
6. Which enrollment type protects user privacy the most?

---

### Lab 4.4: Create WiFi Configuration Profile

**Objective**: Create and deploy Wi-Fi configuration profiles to automatically connect devices to corporate networks.

**Prerequisites**:
- Completed enrollment labs
- Wi-Fi network details (SSID, password, security type)
- Enrolled test devices

**Estimated Time**: 30 minutes

#### Part 1: Create Wi-Fi Profile for Windows

**Step 1: Gather Wi-Fi Information**

Document your Wi-Fi details:
```
Network Name (SSID): CorpNetwork
Security Type: WPA2-Personal or WPA2-Enterprise
Authentication: PSK (Pre-shared key) or EAP
Password: [If PSK]
Certificate: [If EAP]
```

**Step 2: Create Windows Wi-Fi Profile**

1. Go to `https://endpoint.microsoft.com`

2. Navigate to Devices > Windows > Configuration profiles

3. Click "Create profile"

4. Select:
   ```
   Platform: Windows 10 and later
   Profile type: Templates
   Template name: Wi-Fi
   ```

5. Click "Create"

**Step 3: Configure Basic Settings**

1. Basics:
   ```
   Name: Corporate Wi-Fi - Windows
   Description: Auto-connect to CorpNetwork Wi-Fi
   ```

2. Click "Next"

**Step 4: Configure Wi-Fi Settings**

1. Configuration settings:
   ```
   Wi-Fi type: Basic
   Network name: CorpNetwork
   SSID: CorpNetwork
   Connect automatically: Yes
   Connect when network is in range: Yes
   Connection type: Enterprise
   ```

2. For WPA2-Personal (Pre-shared key):
   ```
   Security type: WPA/WPA2-Personal
   Pre-shared key: [Your Wi-Fi password]
   ```

3. For WPA2-Enterprise (EAP):
   ```
   Security type: WPA-Enterprise
   EAP type: EAP-TLS or PEAP
   Server validation: Configure certificate
   Client authentication: Configure certificate
   ```

4. Proxy settings (if needed):
   ```
   Proxy setting: Manual configuration
   Proxy server address: proxy.company.com:8080
   ```

5. Click "Next"

**Step 5: Assign Profile**

1. Assignments:
   ```
   Included groups: All Users
   Or: Sales Department, Marketing Department
   ```

2. Click "Next"

3. Review settings

4. Click "Create"

**Step 6: Monitor Deployment**

1. Go to the profile you created

2. Click "Device status"

3. Monitor deployment:
   ```
   Assigned: [Number of devices]
   Succeeded: [Devices configured]
   Pending: [Waiting for sync]
   Failed: [Errors]
   ```

#### Part 2: Create Wi-Fi Profile for iOS/iPadOS

**Step 1: Create iOS Wi-Fi Profile**

1. Navigate to Devices > iOS/iPadOS > Configuration profiles

2. Click "Create profile"

3. Select:
   ```
   Platform: iOS/iPadOS
   Profile type: Templates
   Template name: Wi-Fi
   ```

4. Click "Create"

**Step 2: Configure iOS Wi-Fi Settings**

1. Basics:
   ```
   Name: Corporate Wi-Fi - iOS
   Description: Auto-connect to CorpNetwork
   ```

2. Configuration settings:
   ```
   Network name: CorpNetwork
   SSID: CorpNetwork
   Connect automatically: Yes
   Hidden network: No (unless your Wi-Fi is hidden)
   Security type: WPA/WPA2-Personal
   Pre-shared key: [Password]
   ```

3. Proxy (optional):
   ```
   Type: Manual or Automatic
   Server: proxy.company.com
   Port: 8080
   ```

4. Click "Next"

**Step 3: Assign to iOS Devices**

1. Included groups: "All Users" or "BYOD Users"

2. Click "Next" and "Create"

#### Part 3: Create Wi-Fi Profile for Android

**Step 1: Create Android Wi-Fi Profile**

1. Navigate to Devices > Android > Configuration profiles

2. Click "Create profile"

3. Select:
   ```
   Platform: Android Enterprise
   Profile type: Fully Managed, Dedicated, and Corporate-Owned Work Profile
   Or: Personally-Owned Work Profile
   Template name: Wi-Fi
   ```

4. Click "Create"

**Step 2: Configure Android Wi-Fi Settings**

1. Basics:
   ```
   Name: Corporate Wi-Fi - Android
   Description: Auto-connect to corporate network
   ```

2. Configuration settings:
   ```
   Network name: CorpNetwork
   SSID: CorpNetwork
   Connect automatically: Enable
   Hidden network: Disable
   Wi-Fi type: Enterprise
   EAP type: PEAP or TLS (for certificate-based)
   Or: Personal (for PSK)
   ```

3. For Personal (PSK):
   ```
   Security type: WPA/WPA2-Personal
   Pre-shared key: [Password]
   ```

4. Click "Next", assign, and create

#### Part 4: Test Wi-Fi Profile Deployment

**Step 1: Sync Devices**

1. For each enrolled device:
   - Windows: Settings > Accounts > Sync
   - iOS: Company Portal > Devices > Sync
   - Android: Company Portal > Devices > Check status

**Step 2: Verify on Windows**

1. On Windows device, open Settings > Network & Internet > Wi-Fi

2. Verify "CorpNetwork" appears in known networks

3. Verify connection status:
   ```
   Network: CorpNetwork
   Status: Connected automatically
   Managed by: Organization
   ```

4. Right-click network > Properties

5. Verify settings match your profile

**Step 3: Verify on iOS**

1. On iOS device, open Settings > Wi-Fi

2. Verify "CorpNetwork" is connected

3. Tap (i) info button next to network

4. Verify:
   ```
   Auto-Join: On
   Network Profile: Managed
   ```

**Step 4: Verify on Android**

1. On Android device, Settings > Network & Internet > Wi-Fi

2. Verify "CorpNetwork" connected

3. Tap network name

4. Verify auto-connect enabled

**Step 5: Test Auto-Connect**

For each device:
1. Disconnect from Wi-Fi or forget network
2. Sync device with Intune
3. Profile should redeploy
4. Device should auto-connect
5. Verify seamless connection

#### Part 5: Advanced Wi-Fi Scenarios

**Scenario 1: Certificate-Based Wi-Fi (EAP-TLS)**

For enterprise Wi-Fi with RADIUS:

1. First, create SCEP or PKCS certificate profile:
   ```
   Devices > Configuration profiles > Create
   Profile type: Trusted certificate or SCEP certificate
   Upload root CA certificate
   ```

2. Then create Wi-Fi profile:
   ```
   Security type: WPA-Enterprise
   EAP type: EAP-TLS
   Root certificates: [Select certificate profile]
   Client certificate: [Select SCEP profile]
   ```

3. Chain profiles:
   - Certificate profile deployed first
   - Wi-Fi profile references certificate
   - No password needed (uses certificate)

**Scenario 2: Multiple Wi-Fi Networks**

Create separate profiles for:
```
Profile 1: Office Main Network
- SSID: CorpNetwork
- Assign to: All Users

Profile 2: Guest Network
- SSID: GuestWiFi
- Assign to: Visitors group

Profile 3: IoT Devices
- SSID: IoT-Network
- Assign to: Dedicated Devices
```

**Scenario 3: Location-Based Wi-Fi**

Using dynamic groups:

1. Create dynamic device group based on location:
   ```
   Property: physicalLocation
   Operator: Equals
   Value: "Building A"
   ```

2. Create Wi-Fi profile for Building A

3. Assign to location-based group

4. Repeat for other locations

#### Verification

✅ Created Wi-Fi profiles for all platforms  
✅ Profiles successfully deployed  
✅ Devices auto-connect to Wi-Fi  
✅ Verified profile deployment status  
✅ Understand certificate-based Wi-Fi  
✅ Know how to troubleshoot Wi-Fi profiles

#### Troubleshooting

**Issue: Wi-Fi profile not deploying**
```
Solutions:
1. Check profile assignment (correct groups)
2. Verify device synced recently
3. Check for profile conflicts
4. Review device configuration logs
5. Ensure SSID spelling is exact
```

**Issue: Device won't auto-connect**
```
Solutions:
1. Verify "Connect automatically" enabled in profile
2. Check if network is in range
3. Remove any manually configured network with same SSID
4. Restart device
5. Redeploy profile
```

**Issue: "Cannot connect to network"**
```
Solutions:
1. Verify SSID is correct (case-sensitive)
2. Check security type matches AP configuration
3. Verify password is correct (PSK)
4. For enterprise, check certificate deployment
5. Test with manual connection first
```

#### Lab Questions

1. What information do you need to create a Wi-Fi profile?
2. What's the difference between WPA2-Personal and WPA2-Enterprise?
3. Why use certificate-based authentication?
4. How do you verify a Wi-Fi profile deployed successfully?
5. Can you deploy multiple Wi-Fi profiles to the same device?

---

### Lab 4.5: Create Device Compliance Policy

**Objective**: Create compliance policies that enforce security requirements and integrate with Conditional Access.

**Prerequisites**:
- Enrolled devices from previous labs
- Understanding of compliance vs configuration

**Estimated Time**: 45 minutes

#### Part 1: Create Windows Compliance Policy

**Step 1: Plan Your Compliance Requirements**

Document requirements:
```
WINDOWS COMPLIANCE REQUIREMENTS
───────────────────────────────
Operating System:
- Minimum: Windows 10 1909
- Maximum: Windows 11 23H2

Security:
- BitLocker encryption: Required
- Firewall: Enabled
- Antivirus: Required and up-to-date
- Secure Boot: Enabled
- Code Integrity: Enabled

Password:
- Required: Yes
- Minimum length: 8 characters
- Password type: Alphanumeric

Device Health:
- Device threat level: Medium or below
- Require device to be at or under Device Threat Level
```

**Step 2: Create Windows Compliance Policy**

1. Go to `https://endpoint.microsoft.com`

2. Navigate to Devices > Windows > Compliance policies

3. Click "Create Policy"

4. Select:
   ```
   Platform: Windows 10 and later
   ```

5. Click "Create"

**Step 3: Configure Basic Settings**

1. Basics:
   ```
   Name: Windows Security Baseline Compliance
   Description: Enforces minimum security requirements for Windows devices
   Platform: Windows 10 and later
   ```

2. Click "Next"

**Step 4: Configure Compliance Settings**

1. **Device Health** section:
   ```
   ✅ Require BitLocker: Require
   ✅ Require Secure Boot: Require  
   ✅ Require code integrity: Require
   ```

2. **Device Properties** section:
   ```
   Minimum OS version: 10.0.18363.0 (1909)
   Maximum OS version: [Leave blank or set limit]
   Valid operating system builds: [Leave blank]
   ```

3. **Configuration Manager Compliance**:
   ```
   Leave unchecked (unless using co-management)
   ```

4. **System Security** section:
   ```
   Password:
   - Require a password: Require
   - Required password type: Alphanumeric
   - Minimum password length: 8
   - Minutes of inactivity before password required: 15
   
   Encryption:
   - Encryption of data storage on device: Require
   
   Firewall:
   - Firewall: Require
   
   Antivirus:
   - Require: Require
   - Real-time protection: Require
   ```

5. **Windows Defender** section:
   ```
   ✅ Microsoft Defender Antimalware: Require
   ✅ Microsoft Defender Antimalware minimum version: [Latest]
   ✅ Microsoft Defender Antimalware security intelligence up-to-date: Require
   ✅ Real-time protection: Require
   ```

6. **Microsoft Defender for Endpoint**:
   ```
   Require device to be at or under machine risk score: Medium
   (If Defender for Endpoint is integrated)
   ```

7. Click "Next"

**Step 5: Configure Actions for Noncompliance**

1. Click "Add" to create action schedule:

   **Action 1: Mark device non-compliant**
   ```
   Action: Mark device non-compliant
   Schedule (days after noncompliance): 0
   Message template: Use default
   ```

   **Action 2: Send notification**
   ```
   Action: Send email to end user
   Schedule: 1 day
   Message template: Create new or use default
   Email subject: Your device is not compliant
   Email message: 
   "Your device doesn't meet security requirements.
    Please resolve the following issues:
    - Enable BitLocker encryption
    - Update Windows Defender
    - Enable Firewall
    
    If you need help, contact IT Support."
   ```

   **Action 3: Retire device (Optional - for high security)**
   ```
   Action: Retire the noncompliant device
   Schedule: 30 days
   (Use cautiously - this wipes the device!)
   ```

2. Click "Next"

**Step 6: Assign Policy**

1. Assignments:
   ```
   Included groups: All Users
   Or create specific groups:
   - Corporate Windows Devices
   - Sales Department
   ```

2. Excluded groups (optional):
   ```
   - IT Testing Devices
   - Exempted Devices
   ```

3. Click "Next"

**Step 7: Review and Create**

1. Review all settings

2. Click "Create"

3. Policy is now active!

#### Part 2: Create iOS/iPadOS Compliance Policy

**Step 1: Create iOS Compliance Policy**

1. Navigate to Devices > iOS/iPadOS > Compliance policies

2. Click "Create Policy"

3. Select platform: iOS/iPadOS

4. Click "Create"

**Step 2: Configure iOS Compliance Settings**

1. Basics:
   ```
   Name: iOS Security Compliance
   Description: Security requirements for iOS/iPadOS devices
   ```

2. **Device Health** settings:
   ```
   ✅ Require jailbroken device detection: Block
   ```

3. **Device Properties**:
   ```
   Minimum OS version: 15.0
   Maximum OS version: [Leave blank]
   Minimum OS build version: [Optional]
   Maximum OS build version: [Optional]
   ```

4. **System Security**:
   ```
   Password:
   - Require a password: Require
   - Required password type: Numeric or Alphanumeric
   - Minimum password length: 6
   - Number of sign-in failures before wiping device: 10
   - Minutes of inactivity before password is required: 5
   
   Restriction:
   - Block simple passwords: Require
   
   Device Security:
   - Block installing apps from untrusted sources: Require (for supervised)
   ```

5. **Microsoft Defender** (if available):
   ```
   Require device to be at or under Device Threat Level: Medium
   ```

6. Click "Next"

**Step 3: Configure iOS Noncompliance Actions**

1. Default action: Mark non-compliant immediately

2. Add notification:
   ```
   Schedule: 1 day
   Action: Send email
   Subject: iOS Device Compliance Issue
   Message: "Please update your iOS version and set a passcode"
   ```

3. Click "Next"

**Step 4: Assign and Create**

1. Assign to: All Users or BYOD Users

2. Review and Create

#### Part 3: Create Android Compliance Policy

**Step 1: Create Android Compliance Policy**

1. Navigate to Devices > Android > Compliance policies

2. Click "Create Policy"

3. Select:
   ```
   Platform: Android Enterprise
   Profile type: Work profile or Fully managed
   ```

4. Click "Create"

**Step 2: Configure Android Compliance**

1. Basics:
   ```
   Name: Android Enterprise Compliance
   Description: Security requirements for Android devices
   ```

2. **Device Health**:
   ```
   ✅ Require device to be rooted: Block
   ✅ Require Play Integrity verdict: Check basic integrity
   ✅ Require Google Play Services: Require
   ```

3. **Device Properties**:
   ```
   Minimum OS version: 10.0
   Maximum OS version: [Leave blank]
   Minimum security patch level: [e.g., 2024-01-01]
   ```

4. **System Security**:
   ```
   Password:
   - Require a password: Require
   - Required password type: At least numeric
   - Minimum password length: 6
   - Number of days until password expires: 90
   - Number of previous passwords to prevent reuse: 5
   - Minutes of inactivity before password required: 15
   
   Encryption:
   - Encryption of data storage: Require
   ```

5. **Microsoft Defender**:
   ```
   Require device to be at or under Device Threat Level: Medium
   ```

6. Click "Next"

**Step 3: Configure Android Noncompliance Actions**

1. Actions:
   ```
   Day 0: Mark non-compliant
   Day 1: Send notification
   Day 7: Send reminder
   Day 30: Remote lock (if enabled)
   ```

2. Assign to Android devices group

3. Create

#### Part 4: Monitor Compliance Status

**Step 1: View Compliance Dashboard**

1. Go to Devices > Monitor > Device compliance

2. Review dashboard:
   ```
   Total devices: [Count]
   Compliant: [Count] [Percentage]
   Not compliant: [Count] [Percentage]
   In grace period: [Count]
   Not evaluated: [Count]
   ```

3. Click on tiles to drill down

**Step 2: Review Non-Compliant Devices**

1. Click "Not compliant" tile

2. See list of non-compliant devices with reasons:
   ```
   Device Name | User | Reason
   ───────────────────────────────────
   PC-001 | John | BitLocker not enabled
   iPhone-002 | Jane | OS version too old
   Android-003 | Bob | Device is rooted
   ```

3. Click on device to see details

**Step 3: Review Compliance by Policy**

1. Go to Devices > Compliance policies

2. Click on a policy

3. Click "Device status"

4. View per-device compliance:
   ```
   Succeeded: [Devices meeting all requirements]
   Error: [Devices with evaluation errors]
   Conflict: [Devices with policy conflicts]
   ```

**Step 4: Generate Compliance Report**

1. Go to Reports > Device compliance

2. Select "Devices without compliance policy"

3. Click "Generate report"

4. Export to CSV for documentation

#### Part 5: Integrate with Conditional Access

**Step 1: Create Conditional Access Policy**

1. Go to `https://portal.azure.com`

2. Navigate to Azure AD > Security > Conditional Access

3. Click "New policy"

4. Configure:
   ```
   Name: Require Compliant Devices
   
   Assignments:
   - Users: All users
   - Cloud apps: Office 365
   
   Conditions:
   - Device platforms: Select all
   
   Access controls:
   - Grant: Require device to be marked as compliant
   ```

5. Enable policy: Report-only (for testing)

6. Click "Create"

**Step 2: Test Compliance Integration**

1. On a non-compliant device:
   - Try accessing Office 365
   - Should be blocked (after report-only testing)

2. Fix compliance issues:
   - Enable BitLocker
   - Update OS
   - Set password

3. Sync device:
   - Wait for compliance evaluation (up to 8 hours)
   - Or force sync

4. Try access again:
   - Should be granted once compliant

**Step 3: Monitor Conditional Access**

1. In Azure AD, go to Sign-ins

2. Review sign-in logs:
   ```
   User | App | Status | Reason
   ──────────────────────────────────
   John | Office 365 | Failure | Device not compliant
   Jane | Office 365 | Success | All checks passed
   ```

3. Click on failed sign-in to see details

#### Verification

✅ Created compliance policies for all platforms  
✅ Configured security requirements  
✅ Set up noncompliance actions  
✅ Monitored compliance dashboard  
✅ Identified non-compliant devices  
✅ Created Conditional Access policy  
✅ Tested compliance integration  
✅ Generated compliance reports

#### Troubleshooting

**Issue: Device showing as "Not evaluated"**
```
Solutions:
1. Device hasn't synced yet - wait or force sync
2. No compliance policy assigned
3. Policy assignment still processing
4. User not in assigned group
```

**Issue: Compliant device marked non-compliant**
```
Solutions:
1. Check evaluation time (can take 8 hours)
2. Force device sync
3. Review policy settings for conflicts
4. Check device logs for specific failure
5. Verify all requirements actually met
```

**Issue: Conditional Access not blocking non-compliant devices**
```
Solutions:
1. Verify Conditional Access policy is "On" (not Report-only)
2. Check policy assignments (users and apps)
3. Ensure "Require compliant device" is selected
4. Wait for policy propagation (15-30 minutes)
5. Check sign-in logs for actual block reason
```

#### Lab Questions

1. What's the difference between compliance and configuration policies?
2. How long does it take for compliance status to update?
3. What happens when a device becomes non-compliant?
4. How does Conditional Access use compliance status?
5. What actions can you take for non-compliant devices?
6. Why might a compliant device show as non-compliant?

---

### Lab 4.6: Configure Windows Update for Business

**Objective**: Implement Windows Update for Business to control how and when devices receive updates.

**Prerequisites**:
- Enrolled Windows 10/11 devices
- Understanding of Windows update types

**Estimated Time**: 40 minutes

#### Part 1: Understand Update Rings Strategy

**Planning Your Update Strategy**:

```
┌─────────────────────────────────────────┐
│     RECOMMENDED UPDATE RING SETUP        │
├─────────────────────────────────────────┤
│                                          │
│  RING 1: IT Pilot (5% of devices)       │
│  Purpose: Early testing                 │
│  Defer feature: 0 days                  │
│  Defer quality: 0 days                  │
│  Target: IT department                  │
│                                          │
│  RING 2: Early Adopters (20%)           │
│  Purpose: Broader testing               │
│  Defer feature: 30 days                 │
│  Defer quality: 7 days                  │
│  Target: Tech-savvy users               │
│                                          │
│  RING 3: General (70%)                  │
│  Purpose: Mass deployment               │
│  Defer feature: 90 days                 │
│  Defer quality: 14 days                 │
│  Target: All users                      │
│                                          │
│  RING 4: Critical (5%)                  │
│  Purpose: Maximum stability             │
│  Defer feature: 180 days                │
│  Defer quality: 21 days                 │
│  Target: Production systems             │
│                                          │
└─────────────────────────────────────────┘
```

#### Part 2: Create Update Ring for IT Pilot

**Step 1: Create First Update Ring**

1. Go to `https://endpoint.microsoft.com`

2. Navigate to Devices > Windows > Windows 10 and later updates

3. Click "Create profile"

4. Select "Windows 10 and later" > "Update rings"

5. Click "Create"

**Step 2: Configure Basics**

1. Basics:
   ```
   Name: Ring 1 - IT Pilot
   Description: First to receive updates for testing
   ```

2. Click "Next"

**Step 3: Configure Update Settings**

1. **Update ring settings**:

   ```
   Feature updates:
   - Feature update deferral period (days): 0
   - Set feature update uninstall period (2-60 days): 10
   
   Quality updates:
   - Quality update deferral period (days): 0
   - Set quality update uninstall period (2-30 days): 7
   
   Other options:
   - Microsoft product updates: Allow
   - Windows drivers: Allow
   - Quality update deadline (days): 3
   - Restart grace period (days): 2
   ```

2. **User experience settings**:

   ```
   Automatic update behavior: Auto install at maintenance time
   Active hours start: 8 AM
   Active hours end: 6 PM
   Restart checks: ✅ Allow
   Option to pause updates: ✅ Enable
   Option to check for Windows updates: ✅ Enable
   Change notification update level: Use the default Windows Update notifications
   Deadline for feature updates (days): 7
   Deadline for quality updates (days): 3
   Grace period (days): 2
   Auto reboot before deadline: Yes
   ```

3. Click "Next"

**Step 4: Assign to IT Pilot Group**

1. Assignments:
   ```
   Included groups: IT Administrators
   Excluded groups: None
   ```

2. Click "Next"

3. Review and Create

#### Part 3: Create Additional Update Rings

**Step 1: Create Early Adopters Ring**

1. Click "Create profile" again

2. Basics:
   ```
   Name: Ring 2 - Early Adopters
   Description: Second wave for broader testing
   ```

3. Update settings:
   ```
   Feature update deferral: 30 days
   Quality update deferral: 7 days
   Quality deadline: 7 days
   Feature deadline: 14 days
   Grace period: 3 days
   ```

4. Assign to: "Sales Department" or create "Early Adopters" group

5. Create

**Step 2: Create General Deployment Ring**

1. Create new profile

2. Basics:
   ```
   Name: Ring 3 - General Deployment
   Description: Standard update timeline for most users
   ```

3. Update settings:
   ```
   Feature update deferral: 90 days
   Quality update deferral: 14 days
   Quality deadline: 10 days
   Feature deadline: 21 days
   Grace period: 5 days
   Active hours: 8 AM - 6 PM
   Restart behavior: Auto install and restart at maintenance time
   ```

4. Assign to: "All Users"

5. Create

**Step 3: Create Critical Systems Ring**

1. Create new profile

2. Basics:
   ```
   Name: Ring 4 - Critical Systems
   Description: Maximum stability for production systems
   ```

3. Update settings:
   ```
   Feature update deferral: 180 days (maximum)
   Quality update deferral: 21 days
   Quality deadline: 14 days
   Feature deadline: 30 days
   Grace period: 7 days
   Allow user to pause: Disable
   Allow user to check: Disable
   ```

4. Assign to: Create "Critical Systems" group

5. Create

#### Part 4: Configure Feature Update Policies

**Step 1: Create Feature Update Policy**

1. In Windows updates, click "Feature updates"

2. Click "Create profile"

3. Basics:
   ```
   Name: Windows 11 23H2 Deployment
   Description: Control Windows 11 version deployment
   ```

**Step 2: Configure Feature Update Version**

1. Feature update settings:
   ```
   Feature update to deploy: Windows 11, version 23H2
   Rollout options:
   - Make update available as soon as possible: No
   - Rollout start date: [Select date 30 days from now]
   - Gradual rollout: 7 days
   ```

2. Deployment settings:
   ```
   Deadline for installation: 14 days after rollout starts
   Deadline for restart: 2 days after installation
   Grace period before restart: 2 days
   ```

3. Click "Next"

**Step 3: Assign Feature Update**

1. Assign to specific groups:
   ```
   Ring 1 - IT Pilot: Immediate
   Ring 2 - Early Adopters: +30 days
   Ring 3 - General: +90 days
   ```

2. Use different policies for each ring

3. Create

#### Part 5: Monitor Update Compliance

**Step 1: View Update Reports Dashboard**

1. Go to Reports > Windows updates

2. Review available reports:
   ```
   - Windows feature updates
   - Windows quality updates
   - Windows Expedited updates
   - Feature update devices report
   - Feature update failures report
   ```

**Step 2: Check Feature Update Status**

1. Click "Windows feature updates"

2. Select your policy

3. Click "View report"

4. Review deployment status:
   ```
   Status Distribution:
   - Up to date: [Count]
   - In progress: [Count]
   - Alert: [Count]
   - Error: [Count]
   
   Version Distribution:
   - Windows 11 23H2: [Count]
   - Windows 11 22H2: [Count]
   - Windows 10 22H2: [Count]
   ```

**Step 3: Check Quality Update Status**

1. Click "Windows quality updates"

2. Review:
   ```
   Last 30 days:
   - Devices updated: [Percentage]
   - Devices pending: [Count]
   - Devices with errors: [Count]
   
   By Update Ring:
   - IT Pilot: 100% updated
   - Early Adopters: 95% updated
   - General: 80% updated
   - Critical: 60% updated (expected - higher deferral)
   ```

**Step 4: Identify Update Issues**

1. Click "Feature update failures report"

2. Review devices with errors:
   ```
   Device | User | Error | Last Update Attempt
   ────────────────────────────────────────────
   PC-001 | John | 0x80070002 | 2 hours ago
   PC-002 | Jane | 0x8007000D | 1 day ago
   ```

3. Click on device for details

4. Review error codes and remediation steps

#### Part 6: Configure Expedited Updates

**What are Expedited Updates?**
- Deploy critical security updates immediately
- Bypass deferral periods
- Used for zero-day vulnerabilities
- Override update rings temporarily

**Step 1: Create Expedited Update**

1. In Windows updates, click "Expedited quality updates"

2. Click "Create profile"

3. Basics:
   ```
   Name: Critical Security Patch - January 2026
   Description: Emergency patch for CVE-2026-XXXXX
   ```

**Step 2: Configure Expedited Settings**

1. Quality update settings:
   ```
   Quality update: [Select latest security update]
   Days until installation is forced: 1
   Days until restart is forced: 0
   ```

2. This bypasses all deferrals!

**Step 3: Assign Expedited Update**

1. Assign to:
   ```
   All devices (for critical vulnerabilities)
   Or specific high-risk devices
   ```

2. Create

3. Monitor deployment closely

#### Part 7: Configure Driver Updates

**Step 1: Create Driver Update Policy**

1. Navigate to Devices > Windows > Driver updates

2. Click "Create profile"

3. Basics:
   ```
   Name: Automatic Driver Updates
   Description: Allow automatic driver updates from Windows Update
   ```

**Step 2: Configure Driver Settings**

1. Settings:
   ```
   Approval type: Automatic
   Deployment period: 7 days
   Deferral period: 0 days
   ```

2. Driver categories:
   ```
   ✅ Audio
   ✅ Camera
   ✅ Disk drives
   ✅ Display
   ✅ Keyboard
   ✅ Mouse
   ✅ Network adapters
   ✅ System devices
   ❌ Firmware (exclude for stability)
   ```

**Step 3: Assign Driver Policy**

1. Assign to: All Users or specific groups

2. Create

#### Part 8: Best Practices Documentation

Create this reference document:

```
WINDOWS UPDATE FOR BUSINESS - BEST PRACTICES
─────────────────────────────────────────────

RING STRATEGY
─────────────
✅ DO:
- Test on IT devices first (Ring 1)
- Gradually expand deployment
- Monitor each ring before proceeding
- Document ring membership criteria
- Review update history monthly

❌ DON'T:
- Deploy to all devices simultaneously
- Skip pilot testing
- Ignore error reports
- Set very short deadlines for critical systems

DEFERRAL PERIODS
────────────────
Feature Updates:
- Minimum: 0 days (pilot)
- Standard: 60-90 days (production)
- Maximum: 180 days (critical systems)

Quality Updates:
- Minimum: 0 days (pilot)
- Standard: 7-14 days (production)
- Maximum: 30 days (critical systems)

DEADLINES AND GRACE PERIODS
────────────────────────────
Recommended Settings:
- Quality deadline: 7 days for most users
- Feature deadline: 14-21 days
- Grace period: 2-3 days (allows user to reschedule)
- Restart deadline: 2 days after installation

Active Hours:
- Configure based on business hours
- Typical: 8 AM - 6 PM
- Consider shift workers

MONITORING
──────────
Weekly Tasks:
- Review update compliance dashboard
- Check for failed updates
- Monitor ring progression
- Review user feedback

Monthly Tasks:
- Analyze update trends
- Adjust ring assignments
- Review and update policies
- Document lessons learned

TROUBLESHOOTING
───────────────
Common Issues:
1. Update failed: Check error codes, free space
2. Restart pending: Verify grace periods
3. Update stuck: Force sync, restart Windows Update service
4. Ring conflicts: Check policy assignments

Emergency Actions:
- Pause updates (if critical issue found)
- Deploy expedited update (for security)
- Adjust deadlines (for flexibility)
```

#### Part 9: Advanced Scenarios

**Scenario 1: Pause Updates for Business Event**

```
Situation: Company conference, don't want updates/restarts

Solution:
1. Create temporary update ring:
   Name: "Conference Week - No Updates"
   Feature deferral: 7 days
   Quality deferral: 7 days
   Deadline: 30 days (no enforcement)

2. Assign to conference attendees

3. Remove after event
```

**Scenario 2: Block Specific Feature Update**

```
Situation: Known issue with Windows 11 23H2

Solution:
1. Don't create Feature Update policy for 23H2
2. Leave devices on 22H2
3. Use update rings to defer feature updates
4. Monitor Microsoft release notes
5. Deploy when issue resolved
```

**Scenario 3: Pilot to Production Workflow**

```
Week 1: IT Pilot (Ring 1)
- Deploy immediately
- Monitor for issues
- Test critical apps
- Document findings

Week 2-3: Early Adopters (Ring 2)
- If pilot successful, expand
- Monitor user feedback
- Track application compatibility

Week 4-6: General Deployment (Ring 3)
- Gradual rollout
- Continue monitoring
- Be ready to pause if issues arise

Week 7+: Critical Systems (Ring 4)
- Only after proven stable
- Scheduled maintenance window
- Extra caution
```

#### Verification

✅ Created 4 update rings with appropriate deferrals  
✅ Configured feature update policies  
✅ Set up quality update management  
✅ Implemented expedited updates capability  
✅ Configured driver update policies  
✅ Monitored update compliance  
✅ Documented best practices  
✅ Understand ring-based deployment strategy  
✅ Can troubleshoot common update issues

#### Troubleshooting

**Issue: Updates not deploying to devices**
```
Solutions:
1. Verify device is in assigned group
2. Check update ring assignment
3. Ensure device has synced recently
4. Verify no blocking policies
5. Check Windows Update service on device
6. Review update ring conflicts
```

**Issue: Devices stuck "Downloading update"**
```
Solutions:
1. Check internet connectivity
2. Verify Windows Update service running
3. Clear Windows Update cache:
   - Stop wuauserv
   - Delete C:\Windows\SoftwareDistribution
   - Restart wuauserv
4. Check disk space (20GB+ required)
5. Review firewall rules
```

**Issue: Update installed but compliance not updated**
```
Solutions:
1. Force Intune sync on device
2. Wait for compliance evaluation (up to 8 hours)
3. Check device reporting in Intune
4. Verify update ring assignment correct
5. Review policy conflicts
```

**Issue: Users constantly postponing restarts**
```
Solutions:
1. Reduce grace period
2. Shorten deadline
3. Configure active hours correctly
4. Use forced restart after deadline
5. Send user communication about importance
6. Consider maintenance windows
```

#### Lab Questions

1. What's the difference between feature and quality updates?
2. What is the maximum deferral period for quality updates?
3. Why create multiple update rings?
4. When would you use an expedited update?
5. What happens when a deadline is reached?
6. How do grace periods work?
7. What is the recommended deferral for production systems?

---

## 📝 Knowledge Check Quiz

### Question 1
What's the key difference between Android Work Profile and Fully Managed?

A) Work Profile is faster  
B) Work Profile separates work/personal data on BYOD devices  
C) Fully Managed is only for Samsung  
D) No difference

<details>
<summary>Click to reveal answer</summary>
**Answer: B** - Work Profile creates isolation on personal devices, while Fully Managed controls the entire corporate-owned device.
</details>

### Question 2
What is the maximum deferral period for Windows quality updates?

A) 7 days  
B) 15 days  
C) 30 days  
D) 365 days

<details>
<summary>Click to reveal answer</summary>
**Answer: C** - Quality updates can be deferred up to 30 days.
</details>

### Question 3
What does a compliance policy do?

A) Configures device settings  
B) Evaluates if device meets requirements  
C) Installs applications  
D) Enrolls devices

<details>
<summary>Click to reveal answer</summary>
**Answer: B** - Compliance policies evaluate device state against requirements and report compliance status.
</details>

### Question 4
Which iOS enrollment type provides the least management?

A) Device Enrollment  
B) User Enrollment  
C) Automated Device Enrollment  
D) All provide the same level

<details>
<summary>Click to reveal answer</summary>
**Answer: B** - User Enrollment manages only work data and is the least invasive option.
</details>

### Question 5
What is the purpose of an Update Ring?

A) To block all updates  
B) To control when different device groups receive updates  
C) To speed up updates  
D) To update drivers only

<details>
<summary>Click to reveal answer</summary>
**Answer: B** - Update rings control the timing of update deployment to different groups of devices.
</details>

### Question 6
What happens when a device is marked non-compliant?

A) It's immediately wiped  
B) Nothing, it's just a status  
C) It can be blocked from resources via Conditional Access  
D) It's automatically fixed

<details>
<summary>Click to reveal answer</summary>
**Answer: C** - Non-compliant status can trigger Conditional Access policies to block access to corporate resources.
</details>

### Question 7
What does Azure AD Join do?

A) Adds a user to Azure AD  
B) Registers device identity in Azure AD for management  
C) Creates a group  
D) Installs applications

<details>
<summary>Click to reveal answer</summary>
**Answer: B** - Azure AD Join registers the device in Azure AD and enables Intune management.
</details>

### Question 8
What is BitLocker?

A) An app blocker  
B) Drive encryption for Windows  
C) A firewall  
D) An antivirus

<details>
<summary>Click to reveal answer</summary>
**Answer: B** - BitLocker provides full disk encryption for Windows devices.
</details>

---

## 📚 Additional Resources

### Microsoft Documentation
- [Windows enrollment methods](https://docs.microsoft.com/mem/intune/enrollment/windows-enrollment-methods)
- [Android Enterprise enrollment](https://docs.microsoft.com/mem/intune/enrollment/android-enroll)
- [iOS enrollment overview](https://docs.microsoft.com/mem/intune/enrollment/ios-enroll)
- [Device configuration profiles](https://docs.microsoft.com/mem/intune/configuration/device-profiles)
- [Compliance policies](https://docs.microsoft.com/mem/intune/protect/device-compliance-get-started)
- [Windows Update for Business](https://docs.microsoft.com/windows/deployment/update/waas-manage-updates-wufb)

### Video Tutorials
- Windows Autopilot Deep Dive
- Android Enterprise Enrollment Methods
- iOS User vs Device Enrollment
- Creating Configuration Profiles
- Implementing Compliance Policies

### Tools
- [Windows Configuration Designer](https://www.microsoft.com/store/productId/9NBLGGH4TX22)
- [Get-WindowsAutopilotInfo script](https://www.powershellgallery.com/packages/Get-WindowsAutopilotInfo)

---

## ✅ Module Completion Checklist

Before moving to Module 5, ensure you have:

- [ ] Enrolled Windows device via Azure AD Join
- [ ] Created and tested provisioning package
- [ ] Enrolled Android device with Work Profile
- [ ] Enrolled Android device as Fully Managed
- [ ] Enrolled iOS device with User Enrollment
- [ ] Enrolled iOS device with Device Enrollment
- [ ] Created Wi-Fi configuration profiles for all platforms
- [ ] Deployed Wi-Fi profiles successfully
- [ ] Created compliance policies for Windows, iOS, and Android
- [ ] Configured noncompliance actions
- [ ] Integrated compliance with Conditional Access
- [ ] Created Windows Update rings (4 rings)
- [ ] Configured feature update policies
- [ ] Monitored update compliance
- [ ] Tested all configurations
- [ ] Documented your environment

---

## 🎯 What's Next?

In **Module 5 - Application Management**, you'll learn:
- Deploying applications across all platforms
- Win32 app packaging and deployment
- App protection policies (MAM)
- App configuration policies
- Managing Microsoft 365 Apps
- App deployment strategies

**Estimated Time for Module 5**: 4-5 hours

---

## 💡 Key Takeaways

✅ **Enrollment Methods** = Different approaches for different ownership scenarios  
✅ **Work Profile** = Best for BYOD, separates work/personal  
✅ **Fully Managed** = Best for corporate-owned, full control  
✅ **Configuration Profiles** = Apply settings, doesn't enforce  
✅ **Compliance Policies** = Evaluate and report device state  
✅ **Conditional Access** = Enforces compliance requirements  
✅ **Update Rings** = Staged deployment strategy  
✅ **Feature Updates** = Major Windows versions, defer up to 365 days  
✅ **Quality Updates** = Security patches, defer up to 30 days  
✅ **Expedited Updates** = Emergency deployment, bypasses deferrals  

---

**Module Status**: ✅ Complete  

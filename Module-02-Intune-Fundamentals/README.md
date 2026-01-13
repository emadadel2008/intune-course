# Module 2 - Intune Fundamentals

## 📋 Module Overview

In this module, you'll dive deep into the fundamentals of Microsoft Intune. You'll understand the differences between various Microsoft management solutions, explore core components, and learn about licensing requirements. This foundation is critical for making informed decisions about your device management strategy.

## 🎯 Learning Objectives

By the end of this module, you will be able to:
- Distinguish between Intune, Configuration Manager, and Microsoft Endpoint Manager
- Identify when to use each management solution
- Understand Intune's core components and architecture
- Recognize different licensing options and requirements
- Make informed decisions about subscription choices
- Plan for co-management scenarios

## 📚 Module Content

### 2.1 Understanding Microsoft's Device Management Evolution

#### The Evolution Timeline

```
2007 ─────► 2011 ─────► 2019 ─────► 2024
SCCM        Intune      Endpoint     Modern
Created     Launched    Manager      Cloud
                        Announced    First
```

**System Center Configuration Manager (SCCM/ConfigMgr)**
- Released: 2007
- Focus: On-premises Windows device management
- Strength: Deep Windows control, complex deployments

**Microsoft Intune**
- Released: 2011
- Focus: Cloud-based, mobile-first management
- Strength: Multi-platform, modern devices, BYOD

**Microsoft Endpoint Manager**
- Announced: 2019
- Focus: Unified management platform
- Includes: Intune + Configuration Manager + Co-management + Analytics

### 2.2 Intune vs Configuration Manager vs Microsoft Endpoint Manager

#### High-Level Comparison

| Feature | Configuration Manager | Microsoft Intune | Microsoft Endpoint Manager |
|---------|----------------------|------------------|---------------------------|
| **Deployment** | On-premises | Cloud-based | Hybrid (Both) |
| **Infrastructure** | Requires servers, SQL | No infrastructure | Flexible |
| **Primary Use** | Corporate networks | Internet-connected | Both scenarios |
| **Device Types** | Windows (primarily) | All platforms | All platforms |
| **Management Style** | Device-centric | User-centric | Both |
| **Network Requirement** | Domain/VPN | Internet only | Flexible |
| **Initial Cost** | High (servers, licenses) | Low (subscription) | Variable |
| **Best For** | Large on-prem estates | Modern, mobile workforce | Hybrid environments |

#### Detailed Feature Comparison

**Configuration Manager Strengths:**
- ✅ Deep Windows OS control
- ✅ Complex task sequences
- ✅ Operating system deployment (OSD)
- ✅ Software distribution with advanced targeting
- ✅ Detailed hardware inventory
- ✅ Software metering and licensing
- ✅ Wake-on-LAN and scheduled deployments
- ✅ Offline deployment capabilities
- ✅ Peer-to-peer content distribution
- ❌ Requires on-premises infrastructure
- ❌ Limited mobile device support
- ❌ Requires VPN for remote devices

**Microsoft Intune Strengths:**
- ✅ Cloud-native (no servers needed)
- ✅ Multi-platform support (Windows, iOS, Android, macOS)
- ✅ Works from anywhere with internet
- ✅ BYOD and MAM capabilities
- ✅ Modern authentication (Azure AD)
- ✅ Rapid deployment and scaling
- ✅ Automatic updates and maintenance
- ✅ Lower total cost of ownership
- ✅ Integration with Microsoft 365
- ❌ Limited offline capabilities
- ❌ Less granular Windows control than ConfigMgr
- ❌ Requires internet connectivity

**Microsoft Endpoint Manager (Combined Solution):**
- ✅ Best of both worlds
- ✅ Co-management capabilities
- ✅ Tenant attach for hybrid scenarios
- ✅ Cloud management gateway
- ✅ Desktop Analytics integration
- ✅ Unified admin console
- ✅ Flexible deployment options

#### When to Use What?

**Use Configuration Manager When:**
- Large on-premises Windows estate (5,000+ devices)
- Complex deployment requirements
- Limited internet connectivity
- Need offline deployment capabilities
- Require detailed software metering
- Already invested in ConfigMgr infrastructure

**Use Microsoft Intune When:**
- Cloud-first strategy
- Multi-platform environment
- Remote/mobile workforce
- BYOD scenarios
- Want to reduce infrastructure
- Modern app deployment (Microsoft Store, Win32)
- Small to medium deployments

**Use Co-Management (Both) When:**
- Transitioning from on-prem to cloud
- Have both scenarios (on-prem + remote)
- Want gradual cloud migration
- Need hybrid capabilities
- Large enterprise with mixed requirements

### 2.3 Core Components of Microsoft Intune

#### Architecture Overview

```
┌────────────────────────────────────────────────────┐
│         Microsoft Endpoint Manager Portal          │
│           (endpoint.microsoft.com)                  │
└───────────────────┬────────────────────────────────┘
                    │
        ┌───────────┴───────────┐
        │                       │
┌───────▼────────┐    ┌────────▼────────┐
│  Azure Active  │    │  Microsoft      │
│  Directory     │◄───┤  Graph API      │
└───────┬────────┘    └────────┬────────┘
        │                      │
        └──────────┬───────────┘
                   │
    ┌──────────────┼──────────────┐
    │              │              │
┌───▼────┐  ┌─────▼─────┐  ┌────▼────┐
│ Intune │  │  Intune   │  │ Intune  │
│  MDM   │  │   MAM     │  │  Apps   │
└───┬────┘  └─────┬─────┘  └────┬────┘
    │             │              │
    └──────┬──────┴──────┬───────┘
           │             │
    ┌──────▼─────┐ ┌────▼──────┐
    │  Managed   │ │ Protected │
    │  Devices   │ │   Apps    │
    └────────────┘ └───────────┘
```

#### Component Breakdown

**1. Azure Active Directory (Identity Layer)**
- User and device identity
- Authentication and authorization
- Conditional Access policies
- Device registration
- Group-based management

**2. Microsoft Intune Service**
- **MDM (Mobile Device Management)**
  - Device enrollment
  - Configuration profiles
  - Compliance policies
  - Device actions (wipe, retire, lock)
  
- **MAM (Mobile Application Management)**
  - App protection policies
  - App configuration policies
  - App deployment and updates
  - Selective wipe

**3. Microsoft Graph API**
- Programmatic access to Intune
- Automation and scripting
- Custom integrations
- Reporting and analytics

**4. Company Portal**
- End-user app (iOS, Android, Windows)
- Self-service device enrollment
- App installation
- Help desk contact information

**5. Intune Management Extension**
- PowerShell script deployment
- Win32 app management
- Proactive remediations
- Custom compliance scripts

**6. Endpoint Analytics**
- Startup performance insights
- Application reliability
- Recommended software
- User experience scoring

#### Data Flow Example

```
User Device ──[1]──► Company Portal App
                           │
                     [2] Enrollment Request
                           │
                           ▼
                  Azure AD Authentication
                           │
                     [3] Device Token
                           │
                           ▼
                   Intune Service (MDM)
                           │
                  [4] Policies & Profiles
                           │
                           ▼
                   Device Configuration
                           │
                  [5] Compliance Check
                           │
                           ▼
                  Conditional Access Gate
                           │
                [6] Access Granted/Denied
```

### 2.4 Licensing and Subscriptions

#### License Tiers

**Microsoft Intune Standalone**
- Price: ~$8/user/month
- Includes:
  - Full MDM capabilities
  - MAM policies
  - Conditional Access (requires Azure AD Premium)
  - Device configuration
  - Compliance policies
  - App deployment
- Best for: Organizations wanting only device management

**Microsoft 365 E3**
- Price: ~$36/user/month
- Includes:
  - Office 365 E3
  - Windows 10/11 Enterprise E3
  - Enterprise Mobility + Security E3
    - Intune
    - Azure AD Premium P1
    - Azure Information Protection P1
- Best for: Most organizations wanting complete productivity suite

**Microsoft 365 E5**
- Price: ~$57/user/month
- Includes:
  - Everything in E3
  - Azure AD Premium P2
  - Microsoft Defender for Endpoint P2
  - Advanced compliance tools
  - Insider risk management
- Best for: Organizations with advanced security needs

**Microsoft 365 Business Premium**
- Price: ~$22/user/month
- Includes:
  - Office 365 Business Premium
  - Intune (with limitations)
  - Azure AD Premium P1
- Limitations:
  - Maximum 300 users
  - Simplified Intune features
- Best for: Small to medium businesses

#### License Comparison Table

| Feature | Intune Standalone | M365 E3 | M365 E5 | M365 Business Premium |
|---------|------------------|---------|---------|----------------------|
| **User Limit** | Unlimited | Unlimited | Unlimited | 300 users max |
| **Device Management** | ✅ Full | ✅ Full | ✅ Full | ✅ Basic |
| **App Management** | ✅ | ✅ | ✅ | ✅ |
| **Conditional Access** | ⚠️ Requires AAD P1 | ✅ P1 | ✅ P2 | ✅ P1 |
| **Windows Autopilot** | ✅ | ✅ | ✅ | ✅ |
| **Endpoint Analytics** | ✅ | ✅ | ✅ | ⚠️ Limited |
| **Microsoft Defender** | ❌ | ⚠️ P1 | ✅ P2 | ❌ |
| **Office 365 Apps** | ❌ | ✅ E3 | ✅ E5 | ✅ Business |
| **Windows Enterprise** | ❌ | ✅ E3 | ✅ E5 | ⚠️ Business |
| **Advanced Compliance** | ❌ | ⚠️ Basic | ✅ Full | ❌ |
| **Monthly Cost/User** | ~$8 | ~$36 | ~$57 | ~$22 |

#### Additional Licensing Considerations

**Device Licenses**
- Can license shared devices instead of users
- Useful for kiosks, labs, shared workstations
- Separate pricing model

**Add-on Licenses**
- **Azure AD Premium P1/P2**: Enhanced identity features
- **Microsoft Defender for Endpoint**: Advanced threat protection
- **Windows 10/11 Enterprise**: OS licensing
- **Intune for Education**: Special pricing for schools

**Trial Licenses**
- Microsoft 365 E5: 30-day trial (25 users)
- Intune Standalone: 30-day trial
- Great for testing and training

#### Cost Calculation Example

**Scenario: 500-user company**

**Option 1: Intune Standalone**
```
500 users × $8 = $4,000/month
+ Azure AD Premium P1 (500 × $6) = $3,000/month
Total: $7,000/month ($84,000/year)
```

**Option 2: Microsoft 365 E3**
```
500 users × $36 = $18,000/month
Total: $18,000/month ($216,000/year)
Includes: Office, Windows, Intune, AAD P1, and more
```

**Option 3: Hybrid (ConfigMgr + Intune)**
```
ConfigMgr licenses (one-time): ~$50,000
+ Infrastructure: ~$30,000
+ Intune (500 × $8): $4,000/month
Total: $80,000 + $48,000/year = $128,000 first year
```

### 2.5 Co-Management Scenarios

#### What is Co-Management?

Co-management enables you to manage Windows 10/11 devices with both Configuration Manager and Microsoft Intune simultaneously.

**Benefits:**
- Gradual cloud transition
- Use ConfigMgr for complex tasks
- Use Intune for cloud benefits
- Flexibility in workload distribution

**Workloads You Can Split:**
1. Compliance policies
2. Resource access policies
3. Windows Update policies
4. Endpoint Protection
5. Device configuration
6. Office Click-to-Run apps
7. Client apps

#### Co-Management Architecture

```
┌──────────────────────────────────────┐
│     Configuration Manager            │
│     (On-Premises Management)         │
└────────────┬─────────────────────────┘
             │
             │ Cloud Management Gateway
             │
             ▼
┌──────────────────────────────────────┐
│      Microsoft Intune                │
│      (Cloud Management)               │
└────────────┬─────────────────────────┘
             │
             │ Co-Management Policies
             │
             ▼
┌──────────────────────────────────────┐
│    Windows 10/11 Devices             │
│    (Managed by Both)                 │
└──────────────────────────────────────┘
```

---

## 🔬 Hands-On Labs

### Lab 2.1: Compare Intune and Configuration Manager

**Objective**: Understand the practical differences between Intune and Configuration Manager through feature comparison and decision-making exercises.

**Prerequisites**:
- Completed Module 1
- Access to Microsoft Endpoint Manager admin center
- Basic understanding of device management concepts

**Estimated Time**: 45 minutes

#### Part 1: Feature Matrix Creation

Create a comprehensive comparison matrix for your organization.

**Step 1: Identify Your Requirements**

Create a document with the following sections:

**Organizational Profile:**
```
Company Size: _____ employees
Number of Devices: _____
Device Mix:
- Windows PCs: ____%
- MacBooks: ____%
- iOS devices: ____%
- Android devices: ____%

Workforce Type:
- Office-based: ____%
- Remote workers: ____%
- Hybrid: ____%

Connectivity:
- Always online: ____%
- Intermittent: ____%
- Mostly offline: ____%
```

**Step 2: Evaluate Management Requirements**

For each requirement, rate importance (1=Low, 5=Critical):

| Requirement | Importance | ConfigMgr | Intune | Notes |
|------------|------------|-----------|--------|-------|
| Multi-platform support | ___ | 2/5 | 5/5 | |
| Works without VPN | ___ | 1/5 | 5/5 | |
| OS deployment | ___ | 5/5 | 2/5 | |
| BYOD support | ___ | 1/5 | 5/5 | |
| Offline deployment | ___ | 5/5 | 1/5 | |
| Low infrastructure cost | ___ | 1/5 | 5/5 | |
| Complex task sequences | ___ | 5/5 | 2/5 | |
| App protection (MAM) | ___ | 1/5 | 5/5 | |
| Software metering | ___ | 5/5 | 2/5 | |
| Cloud-native | ___ | 1/5 | 5/5 | |

**Step 3: Scenario-Based Analysis**

Analyze these scenarios and determine the best solution:

**Scenario A: Global Marketing Firm**
```
- 800 employees across 15 countries
- 60% MacBooks (Creative team)
- 30% Windows laptops (Business team)
- 10% iOS/Android (Mobile workforce)
- Cloud-first strategy
- No on-premises infrastructure
- BYOD policy for mobile devices

Recommended Solution: _______________
Reasoning: _______________________
```

**Scenario B: Manufacturing Company**
```
- 2,500 employees at 5 facilities
- 95% Windows desktops (Factory floor)
- Limited internet at production areas
- Complex custom software deployments
- 24/7 operations requiring scheduled updates
- Existing ConfigMgr infrastructure
- 200 remote sales staff with laptops

Recommended Solution: _______________
Reasoning: _______________________
```

**Scenario C: Healthcare Organization**
```
- 1,200 staff members
- 40% Windows workstations (Admin)
- 30% iPads (Nurses/Doctors)
- 20% iPhones (Mobile staff)
- 10% Android tablets (Patient check-in)
- HIPAA compliance required
- Mix of hospital and clinic locations
- Some staff work remotely

Recommended Solution: _______________
Reasoning: _______________________
```

#### Part 2: Hands-On Comparison

**Step 1: Explore Intune Device Management**

1. Sign in to `https://endpoint.microsoft.com`
2. Navigate to **Devices** > **All devices**
3. Document available management actions:
   ```
   Available Actions:
   - [ ] Sync
   - [ ] Restart
   - [ ] Remote lock
   - [ ] Retire
   - [ ] Wipe
   - [ ] Fresh Start
   - [ ] Autopilot reset
   ```

4. Navigate to **Devices** > **Configuration profiles**
5. Click **Create profile** > Select **Windows 10 and later**
6. Document available profile types:
   ```
   Profile Types Available:
   - [ ] Templates
   - [ ] Settings catalog
   - [ ] Administrative templates
   - [ ] Custom profiles
   ```

**Step 2: Compare Deployment Capabilities**

Create this comparison table:

| Deployment Capability | ConfigMgr | Intune | Verification |
|----------------------|-----------|--------|--------------|
| OS Deployment (bare metal) | ✅ Full | ❌ No | ConfigMgr wins |
| App deployment (MSI) | ✅ | ✅ | Both support |
| App deployment (Win32) | ✅ | ✅ | Both support |
| Store app deployment | ❌ | ✅ | Intune wins |
| Script deployment | ✅ | ✅ | Both support |
| Driver deployment | ✅ Full | ⚠️ Limited | ConfigMgr wins |
| Windows Updates | ✅ Full control | ✅ WUfB | Different approaches |
| Content distribution | ✅ DP/Peer | ☁️ Cloud | Different models |
| Deployment scheduling | ✅ Advanced | ⚠️ Basic | ConfigMgr wins |
| User-based deployment | ✅ | ✅ | Both support |

**Step 3: Analyze Real Deployment Times**

If you have access to both systems, test deployment times:

**Test: Deploy 7-Zip (small app)**
```
Configuration Manager:
- Time to package: ___ minutes
- Distribution to DP: ___ minutes
- Client deployment: ___ minutes
- Total: ___ minutes

Intune:
- Time to upload: ___ minutes
- Client deployment: ___ minutes
- Total: ___ minutes
```

#### Part 3: Cost Analysis Exercise

**Step 1: Calculate 5-Year TCO**

For a 1,000-user organization:

**Configuration Manager Costs:**
```
Year 1:
- Server hardware: $15,000
- SQL licenses: $10,000
- ConfigMgr licenses: $40,000
- Implementation: $25,000
- Training: $5,000
Total Year 1: $95,000

Years 2-5 (annual):
- Maintenance: $5,000
- Support: $10,000
- Updates: $2,000
Annual: $17,000 × 4 = $68,000

5-Year Total: $163,000
```

**Intune Costs:**
```
Year 1:
- Licenses (1000 × $8 × 12): $96,000
- Implementation: $15,000
- Training: $3,000
Total Year 1: $114,000

Years 2-5 (annual):
- Licenses (1000 × $8 × 12): $96,000
Annual: $96,000 × 4 = $384,000

5-Year Total: $498,000
```

**Co-Management Costs:**
```
Year 1:
- Existing ConfigMgr: $0 (already owned)
- Intune licenses: $96,000
- Integration setup: $10,000
Total Year 1: $106,000

Years 2-5 (annual):
- ConfigMgr maintenance: $17,000
- Intune licenses: $96,000
Annual: $113,000 × 4 = $452,000

5-Year Total: $558,000
```

**Analysis:**
- ConfigMgr appears cheaper but doesn't include modern features
- Intune costs are predictable (OPEX vs CAPEX)
- Co-management provides flexibility but highest cost

#### Part 4: Decision Framework

Create a decision tree for your organization:

```
START: Do you need to manage non-Windows devices?
  │
  ├─ YES ──► Do you have existing ConfigMgr?
  │           │
  │           ├─ YES ──► Start with Co-Management
  │           │
  │           └─ NO ──► Use Intune Only
  │
  └─ NO ──► Is internet always available?
              │
              ├─ YES ──► Do you need OS deployment?
              │           │
              │           ├─ YES ──► Consider ConfigMgr
              │           │
              │           └─ NO ──► Use Intune
              │
              └─ NO ──► Must use ConfigMgr
```

#### Verification

✅ You can articulate differences between ConfigMgr and Intune  
✅ You understand when to use each solution  
✅ You can calculate TCO for different scenarios  
✅ You've analyzed scenarios relevant to your environment  
✅ You can make informed recommendations

#### Lab Questions

1. What are three scenarios where ConfigMgr is better than Intune?
2. What are three scenarios where Intune is better than ConfigMgr?
3. When would you recommend co-management?
4. How does licensing cost differ between solutions?
5. What factors influence your choice beyond just features?

---

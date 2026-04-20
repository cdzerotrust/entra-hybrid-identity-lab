# 🔐 Hybrid Identity Deployment with Microsoft Entra Connect Sync

## 📌 Overview
This project demonstrates the deployment and recovery of a **hybrid identity environment** using Microsoft Entra Connect Sync.

The goal was to integrate an on-premises Active Directory (`lab.local`) with Microsoft Entra ID while troubleshooting a failed deployment caused by authentication and configuration issues.

---

## 🎯 Objectives

- Deploy Microsoft Entra Connect Sync
- Enable Password Hash Synchronization (PHS)
- Scope synchronization using OU filtering
- Troubleshoot failed deployment (certificate / MSAL issue)
- Validate identity synchronization in Entra ID

---

## 🏗️ Environment

- **On-Prem AD**: Windows Server 2022 (`lab.local`)
- **Cloud Identity**: Microsoft Entra ID (P2 License)
- **Tooling**: Microsoft Entra Connect Sync
- **Scope**: OU-based filtering (Cloud-Sync OU)

---

## ⚙️ Configuration

### 🔑 Sign-in Method
- Password Hash Synchronization (PHS)
- Seamless Single Sign-On (SSO)

---

### 🗂️ OU Filtering (Security Control)

Scoped synchronization to only required users:

lab.local → Users → Cloud-Sync
![OU Filtering](images/ou-filtering.png)

---

## 🚨 Issue Encountered

Initial deployment failed with an authentication error:

Microsoft.Identity.Client.MsalServiceException
AADSTS700027: Certificate expired

![MSAL Error](images/msal-error.png)

---

## 🔍 Root Cause

- Expired certificate tied to Entra Connect application
- Corrupted installation artifacts
- Stale app registrations in Microsoft Entra ID

---

## 🛠️ Troubleshooting & Recovery

### 🔧 Removed Corrupted Installation

```powershell
Stop-Service ADSync
sc.exe delete ADSync
Remove-Item -Recurse -Force "C:\ProgramData\AADConnect"
Remove-Item -Recurse -Force "C:\Program Files\Microsoft Azure AD Sync"
Remove-Item -Recurse -Force "HKLM:\SOFTWARE\Microsoft\Azure AD Connect"
Remove-Item -Recurse -Force "HKLM:\SOFTWARE\Microsoft\AD Sync"

☁️ Cleaned Entra ID
Removed stale provisioning applications:
ConnectSyncProvisioning_AD-DC01_*
🔄 Reinstalled Entra Connect
Performed clean installation
Reconfigured sync and OU filtering
Re-enabled SSO
✅ Successful Deployment

🔍 Validation
👤 Synced Users

Users successfully synchronized from on-prem AD:

🔎 Identity Verification

Validated for synced users:

On-premises sync: Enabled
Immutable ID present
Distinguished Name mapped from AD

⚠️ Identity Observation

Duplicate accounts observed:

Cloud-only users (pre-existing)
Synced users (from AD)
💡 Insight:

Entra Connect does not automatically merge identities unless matching conditions are met, which can lead to duplicate identities.

🔐 Security Impact

Reduced attack surface using OU-based scoping
Established centralized identity control
Prevented synchronization of unnecessary objects
Enabled foundation for:
Conditional Access
Access Reviews
Privileged Identity Management (PIM)

🧠 Key Takeaways

Hybrid identity failures often stem from certificate or app registration issues
Proper cleanup of services, registry, and Entra objects is critical
OU filtering acts as a security boundary
Validation must be performed in Entra ID, not just locally

🚀 Next Steps

Implement Conditional Access (MFA enforcement)
Configure Access Reviews (Entra ID P2)
Monitor provisioning and sign-in logs
Integrate PIM for privileged roles

👤 Author

Chris Davis
Cloud IAM | Governance, Risk & Compliance (GRC) | Zero Trust Security

# 🛡️ Project: Just-In-Time (JIT) Privileged Access via Microsoft Entra PIM

## Project Overview
In a modern "Zero Trust" environment, standing privileges (accounts that are permanently admins) are a major security liability. This project demonstrates the transition from high-risk permanent administrative rights to a secure Just-In-Time (JIT) model using Microsoft Entra Privileged Identity Management (PIM).

The goal was to ensure that the Global Administrator role is only "Eligible" for use, requiring Multi-Factor Authentication (MFA) and a business justification to activate, with a forced expiration after a set duration.

---

## Technologies & Requirements
* Platform: Microsoft Azure / Entra ID
* Service: Privileged Identity Management (PIM)
* Licensing: Entra ID P2
* Concepts: Least Privilege, JIT Access, Identity Governance

---

## Phase 1: Configuration & Implementation

### 1. Removing "Standing" Access
To start with a Zero Trust baseline, I identified that the `cloudadmin` account held a permanent Global Administrator role. 
* Action: Navigated to Entra ID > Users > Assigned Roles.
* Step: Selected the permanent Global Administrator assignment and clicked Remove. 
* Purpose: To ensure the account has zero administrative power until it is activated via PIM.

> **[Screenshot: image_2e5883.png]**
> *Caption: Identifying the "Permanent" assignment conflict and removing it to enforce the JIT workflow.*

### 2. Configuring Governance Policies
I defined the "guardrails" for the Global Administrator role within the PIM dashboard.
* Activation Settings:
    * Max Duration: 2 Hours.
    * MFA: Required on activation.
    * Justification: Required for audit logs.
    * Approval: Set to "None" for automated JIT in this lab environment.

> **[Screenshot: image_2fc197.png]**
> *Caption: Configuring the PIM role settings to enforce MFA and justification.*

---

## Phase 2: Challenges & Technical Resolutions
During the implementation, several real-world technical hurdles appeared. I documented the troubleshooting process below to demonstrate cloud-admin competency.

| Challenge | Root Cause | Resolution |
| :--- | :--- | :--- |
| "Activate Role Failed" Error | Conflict between a "Permanent" role and an "Eligible" PIM assignment. | **Action:** Deleted the permanent role assignment from the user's main profile. This allowed PIM to become the sole authority for role elevation. |
| Missing "Settings" Tab | UI Update: The Settings menu was not on the main sidebar. | **Action:** Discovered that settings are now located *inside* the specific role. Navigated to PIM > Roles > Global Administrator > Role Settings. |
| "Already Exists" Loop | Browser caching of old security tokens/cookies. | **Action:** Utilized Incognito/InPrivate mode to force a clean session and used `aka.ms/pim/tokenrefresh` to update permissions. |
| Validation Delay | Entra ID propagation lag (5-15 mins). | **Action:** Cleared filters, refreshed the PIM dashboard, and waited for the global catalog to sync. |

> **[Screenshot: image_2e72ca.png]**
> *Caption: The "Role assignment already exists" error encountered during troubleshooting.*

---

## Phase 3: Testing & Final Verification
The final phase was to prove that the "Just-In-Time" mechanism works as intended.

### 1. The Activation Request
As the `cloudadmin` user, I navigated to PIM > My Roles. The Global Administrator role appeared under Eligible Assignments. I clicked Activate and was prompted to:
1.  Complete a fresh MFA challenge.
2.  Provide a Justification: "Activating GA permissions for security policy configuration and audit."

> **[Screenshot: image_2f5060.png]**
> *Caption: The end-user JIT activation screen showing the duration slider and justification box.*

### 2. Final Success Verification
After clicking Activate, the system processed the request. I verified success by checking the Active Assignments tab.
* Role: Global Administrator
* Status: Activated
* End Time: 1 hours 40 minutes (Counting down)

**Verification Result:** The account successfully gained admin rights, but the portal clearly shows these rights are temporary and will vanish automatically when the timer hits zero.

---

## Key Takeaways
* Reduced Attack Surface: Attackers cannot exploit a compromised admin account if that account doesn't have "active" rights.
* Audit Readiness: Every activation is logged with a justification, making security audits much faster.
* Identity as the Perimeter: This project proves that modern security relies on controlling how and when identities are used, rather than just relying on passwords.

---

## Troubleshooting & Collaboration
During this deployment, I encountered several real-world hurdles. I collaborated with an AI assistant to diagnose and resolve these technical roadblocks, simulating a real-world escalation process.

| Challenge | Detection | Resolution |
| :--- | :--- | :--- |
| The "Missing Settings" Menu | Could not find the "Settings" tab under Microsoft Entra roles. | Identified that settings are now nested inside the specific role name rather than on the main sidebar. |
| "Activate Role Failed" Error | Received a red error stating: "The Role assignment already exists." | Removed the permanent assignment, cleared browser cache (Incognito), and forced a token refresh. |
| MFA Loop / Verification Check | The "Activate" button remained greyed out or threw a verification error. | Identified that PIM activation requires a "fresh" MFA claim. Solved by using an InPrivate/Incognito window. |
| Token Sync Latency | Changes were not reflecting in the PIM dashboard immediately. | Implemented a logout/login period and used the token refresh URL to force a sync. |

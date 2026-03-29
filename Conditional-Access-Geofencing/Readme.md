# Conditional Access & Geographic Hardening

## 📖 Project Overview
This module demonstrates the transformation of a Microsoft Entra ID tenant from basic **Security Defaults** to a granular **Zero-Trust Architecture**. By implementing context-aware policies, I established a security model where access is granted based on real-time signals: **Identity, Location, and Role Status.**

---

## 🏗️ Implementation Phase

### 1. Disabling Tenant Security Defaults
To gain granular control over security policies, I first had to transition away from Microsoft's "one-size-fits-all" Security Defaults. This unlocked the ability to create custom Conditional Access policies.
* **Evidence:** ![Security Defaults Warning](Screenshots/Screenshot%202026-03-22%20233824.png)

### 2. Privileged Identity Management (PIM) Configuration
To reduce the attack surface, I moved administrative accounts from "Standing Access" to a **Just-In-Time (JIT)** model.
* **Eligible Status:** Configured the `Cloudadmin` account as "Eligible," meaning it has no permissions until specifically activated.
* **Evidence:** ![PIM Eligibility](Screenshots/Screenshot%202026-03-22%20231136.png)
* **Active Status:** Verified the successful activation of the Global Administrator role.
* **Evidence:** ![PIM Active Assignment](Screenshots/Screenshot%202026-03-22%20235129.png)

### 3. Geographic Perimeters (Geofencing)
I established a network boundary by defining "Poland" as a Named Location. This allows the security engine to treat geographic location as a primary authentication signal.
* **Evidence:** ![Named Locations Config](Screenshots/Screenshot%202026-03-24%20220104.png)

### 4. Zero-Trust Policy Logic (Conditional Access)
I built a policy named **"Block Legacy Authentication"** with the following enforcement rules:
* **Exclusion Logic:** To prevent total tenant lockout, I explicitly excluded my cloud-only **Break-Glass account** (`artwellmaukure@yahoo.com`).
* **Evidence:** ![Policy User Exclusion](Screenshots/Screenshot%202026-03-22%20235811.png)
* **Network Logic:** Configured the policy to include "All Locations" but **Exclude** the trusted "Poland" perimeter.
* **Evidence:** ![Location Inclusion/Exclusion](Screenshots/Screenshot%202026-03-24%20221319.png)

---

## 🧪 Testing and Validation

### Scenario A: The "Deny-by-Default" Validation
I simulated a security breach by attempting to access the tenant without an active PIM session.
* **The Result:** Access was denied. Even though the user was in a trusted location, the **403 Forbidden** error proved that the system correctly defaulted to "Deny" because the administrative identity signal was missing.
* **Evidence:** ![Validation 403 Error](Screenshots/Screenshot%202026-03-23%20003241.png)

### Scenario B: Multifactor Authentication (MFA) Pivot
I updated the policy to move from a hard "Block" to a **Grant + MFA** requirement.
* **The Handshake:** Successfully triggered the Microsoft Authenticator **Number Matching** challenge, ensuring that access is only granted after a second biometric or push-notification factor.
* **Evidence:** ![MFA Number Match Challenge](Screenshots/Screenshot%202026-03-23%20004142.png)
* **Evidence:** ![MFA Verification Success](Screenshots/Screenshot%202026-03-24%20222239.png)

---

## 🧠 Technical Key Takeaways
1. **Dynamic Evaluation:** Conditional Access is not a static gate; it re-evaluates trust every time a signal changes (like when a PIM role expires).
2. **Resiliency Planning:** The use of a "Break-Glass" account is critical when deploying "Block" policies to ensure the environment remains manageable during unforeseen network shifts.
3. **Least Privilege:** By combining PIM and CA, I achieved the "Gold Standard" of IAM: Identity is only privileged when necessary, and only accessible from verified locations.

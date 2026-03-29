## Conditional Access & Geographic Hardening

### Overview
This module demonstrates the implementation of a **Zero Trust network perimeter**. The tenant was transitioned from Security Defaults to granular Conditional Access policies that evaluate user risk based on location and role status.

---

## Implementation Steps

### 1. Disabling Security Defaults
Before creating custom policies, global Security Defaults were disabled.

**Evidence:**  
![Security Defaults](./Screenshots/Screenshot%202026-03-22%20233824.png)

---

### 2. Geographic Hardening
"Poland" was defined as a geographic boundary to distinguish between trusted and untrusted locations.

**Evidence:**  
![Named Locations](./Screenshots/image_1d33c5.png)

---

### 3. Policy Configuration
The **Block Legacy Authentication** policy was configured with exclusions to maintain administrative access.

**Exclusion Logic:**
- Excluded the Poland location  
- Excluded the Break-Glass account  

**Evidence:**  
![Location Exclusion](./Screenshots/image_1cde0b.png)  
![User Exclusion](./Screenshots/Screenshot%202026-03-22%20235811.png)

---

## Testing and Validation

### Scenario A: Security Block (403 Error)
Testing confirmed that when a PIM session expires, the account is blocked.

**Evidence:**  
![403 Access Denied](./Screenshots/image_1cc381.png)

---

### Scenario B: MFA Challenge
The policy was updated to trigger an MFA challenge instead of blocking access.

**Evidence:**  
![MFA Success](./Screenshots/image_1d3fc1.png)

---

## Technical Logic
The Conditional Access engine evaluates sessions in real time. When a role is activated through PIM, elevated privileges are granted. Once the session expires, access is re-evaluated and restricted.

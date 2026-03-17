# Project: Just-In-Time (JIT) Privileged Access via Microsoft Entra PIM

## Project Overview
In a modern Zero Trust environment, standing privileges (accounts that are permanently admins) are a major security liability. This project demonstrates the transition from high-risk permanent administrative rights to a secure Just-In-Time (JIT) model using Microsoft Entra Privileged Identity Management (PIM).

The goal was to ensure that the Global Administrator role is only "Eligible" for use, requiring Multi-Factor Authentication (MFA) and a business justification to activate, with a forced expiration after a set duration.

---

## Technologies & Requirements
* **Platform:** Microsoft Azure / Entra ID
* **Service:** Privileged Identity Management (PIM)
* **Licensing:** Entra ID P2
* **Concepts:** Least Privilege, JIT Access, Identity Governance

---

## Phase 1: Configuration & Implementation

### 1. Initial State Assessment
I audited the `cloudadmin` user and confirmed they held a Permanent "Active" assignment. This represents a high standing access risk that must be migrated to a JIT model.

![Initial Permanent State](./images/Screenshot%202026-03-17%20122705.png)
*Caption: Identifying the initial high-risk 'Permanent' state of the Global Administrator role.*

### 2. Technical Hurdle: Self-Assignment Protection
While attempting to remove the permanent assignment, Azure triggered a safety error: *"You cannot update self assignment for this role"*. This is a built-in guardrail to prevent administrators from accidentally locking themselves out.

* **Resolution:** I utilized a secondary administrator account to modify the `cloudadmin` role assignment.

![Self Assignment Error](./images/Screenshot%202026-03-17%20122941.png)
*Caption: The specific Azure safety error encountered when trying to modify one's own permanent privileges.*

### 3. Transitioning to Eligibility
Using the PIM Quick Start dashboard, I moved the user from an "Active" permanent assignment to an "Eligible" assignment.

![PIM Dashboard](./images/Screenshot%202026-03-17%20110102.png)
*Caption: Navigating the PIM dashboard to manage role eligibility.*

![Setting Eligibility](./images/Screenshot%202026-03-17%20110900.png)
*Caption: Configuring the user as 'Eligible' rather than 'Permanent'.*

### 4. Configuring Governance Policies
I defined strict "guardrails," including a 2-hour maximum duration and mandatory Azure MFA.

![Role Settings](./images/Screenshot%202026-03-17%20130625.png)
*Caption: Defining activation requirements, including mandatory MFA and justification.*

---

## Phase 2: Challenges & Technical Resolutions

| Challenge | Detection | Resolution |
| :--- | :--- | :--- |
| **Self-Assignment Lock** | | Switched to a secondary GA account to modify status. |
| **Search/Filter UI Delay** | | Cleared dashboard filters and refreshed the browser. |
| **"Already Exists" Conflict** | | Deleted redundant permanent assignments. |

---

## Phase 3: Testing & Final Verification

### 1. Requesting Access
The Global Administrator role now correctly appears as an **Eligible assignment**.

![Eligible Status](./images/Screenshot%202026-03-17%20132155.png)
*Caption: The user no longer has standing rights and must 'Activate' to gain them.*

### 2. Justification and Elevation
I initiated activation, providing a mandatory justification: *"Testing JIT"*.

![Activation Request](./images/Screenshot%202026-03-17%20132349.png)
*Caption: The end-user activation screen requiring a duration and business reason.*

### 3. Final Success Verification
The activation was successful. The role is now listed under **Active Assignments** with a specific expiration timestamp.

![Successful JIT Activation](./images/Screenshot%202026-03-17%20132520.png)
*Caption: Verification of JIT success. Privileges are active but temporary.*

---

## Key Takeaways
* **Operational Security:** The self-assignment error proved the necessity of "Break-Glass" accounts.
* **Attack Surface Reduction:** JIT ensures a compromised credential does not grant immediate admin access.

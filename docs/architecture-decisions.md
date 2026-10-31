# Architecture Decision Records

This document captures the key decisions made during the design of the Secure Service Edge solution.

---

## ADR-001: Use only the Microsoft 365 profile of Global Secure Access

**Status:** Accepted

**Context**  
Global Secure Access offers three profiles: Microsoft 365, Private Access, and Internet Access. Enabling all three would have provided broader coverage but would have extended organisational control into personal browsing and non-company resources.

**Decision**  
Only the Microsoft 365 profile was enabled.

**Consequences**  
- Positive: Clearer ownership/privacy boundary. Higher user acceptance.
- Negative: No protection or visibility for non-M365 traffic.
- Risk accepted: Users can still access arbitrary internet destinations outside GSA.

---

## ADR-002: Enable Universal Tenant Access

**Status:** Accepted

**Context**  
The organisation actively collaborates with external partners via Microsoft Teams. Restricting tenant access broke legitimate cross-tenant collaboration.

**Decision**  
Universal tenant access was turned on.

**Consequences**  
- Positive: Partnership workflows continued to function.
- Negative: Expanded the potential attack surface.
- Mitigations: Strong Conditional Access, MFA, device compliance, and malware response controls remained in place. The benefits of GSA were judged to outweigh the additional risk.

---

## ADR-003: Disable Security Defaults in favour of Conditional Access

**Status:** Accepted

**Context**  
Security Defaults provide a baseline but are inflexible. Granular policies (device compliance + GSA network + MFA + named locations) were required.

**Decision**  
Security Defaults were turned off and replaced with targeted Conditional Access policies.

**Consequences**  
- Positive: Precise control over access conditions.
- Negative: Requires ongoing policy hygiene and monitoring.
- Note: This is a common and recommended pattern once an organisation is ready for Conditional Access.

---

## ADR-004: Windows Entra Join vs macOS Registration

**Status:** Accepted

**Context**  
Global Secure Access has different identity requirements across platforms.

**Decision**  
- Windows devices: Full Microsoft Entra ID join
- macOS devices: Device registration (with Apple Push Notification certificate)

**Consequences**  
- Windows provided a stronger device identity signal and smoother GSA experience.
- macOS required additional certificate and configuration profile work.
- Both approaches were made to work within the BYOD constraint (user-driven enrollment).

---

## ADR-005: Offboarding sequence — Retire device before deleting identity

**Status:** Accepted

**Context**  
Simply deleting a user in Entra ID can leave residual device state, tokens, or work profile data.

**Decision**  
Automation was implemented so that when a leaver process is triggered:
1. The device is retired in Intune
2. Then the Entra identity is deleted

**Consequences**  
- Cleaner removal of the work profile
- Reduced risk of residual access
- Better alignment with the separation of personal and work identities on the device

---

## ADR-006: Do not block legacy authentication (initially)

**Status:** Accepted (temporary)

**Context**  
Some SharePoint access paths still depended on legacy authentication at the time of implementation.

**Decision**  
Legacy authentication was not blocked.

**Consequences**  
- Avoided breaking existing SharePoint workflows.
- Accepted a known weaker authentication path as a temporary measure.
- Recommended future work: migrate remaining dependencies and then block legacy authentication.

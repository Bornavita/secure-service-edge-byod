# Production Hardening & Edge Cases

This document records the additional controls implemented to address real-world bypasses, operational risks, and lifecycle edge cases that commonly appear in Microsoft Global Secure Access + Entra ID + Intune deployments.

These were not theoretical considerations — they were actively configured and tested.

---

## 1. Protocol & Network Bypass Mitigations

### QUIC (UDP 443) Bypass
Modern browsers can use QUIC over UDP 443 for Microsoft 365 traffic. When this occurs, traffic may bypass the GSA client tunnel and therefore skip Conditional Access network/location checks.

**Action taken:**  
QUIC was disabled for Edge and Chrome via Intune Administrative Templates (`Allow QUIC protocol = Disabled`).

### IPv6 Direct Routing
GSA primarily handles IPv4 traffic through its tunnel adapters. Native IPv6 connectivity from the user’s ISP can allow M365 destinations to be reached outside the GSA path.

**Action taken:**  
IPv6 bindings were restricted / IPv4 preference enforced through Intune device configuration profiles so that M365 traffic remained on the intended path.

---

## 2. Conditional Access & Resilience

### Report-Only Mode First
New Conditional Access policies were initially deployed in **report-only** mode. This allowed impact assessment and tuning before enforcement, reducing the risk of unexpected lockouts.

### Break-Glass Accounts
Emergency access (break-glass) accounts were excluded from the strict GSA + compliant network Conditional Access policies. This preserved a reliable recovery path in case of misconfiguration or GSA-related authentication issues.

### Offline / Cached Credential Considerations
Strict “must be on GSA network” requirements can create problems when a device is offline or the GSA tunnel cannot yet be established. Cached credential behaviour and break-glass exclusions were used to reduce this risk.

---

## 3. Offboarding Race Condition

A known risk exists when an Entra ID account is deleted before Intune has successfully delivered and executed a Selective Retire / Retire command. Once the account is gone, the management channel needed to wipe the work profile can be lost.

**Action taken:**  
The offboarding sequence was designed and automated so that **device retirement in Intune occurs before the Entra identity is deleted**. This ordering was treated as a hard requirement rather than a best-effort step.

---

## 4. Observability

GSA client logs on endpoints are limited and rotate quickly. For meaningful troubleshooting and auditability, traffic and sign-in signals need to be centralised.

**Direction taken:**  
Entra Network Access / GSA-related logs were considered for streaming into a Log Analytics workspace (or equivalent) to support investigation of silent failures and access blocks.

---

## 5. Additional Controls Already in Place

- Microsoft Defender for Endpoint risk signals used to trigger immediate session impact (sign-out / block) on high-severity detections.
- Microsoft Purview used for detection of sensitive information sharing attempts.
- Notifications configured for unauthorised access attempts.
- MFA via Authenticator enforced as a core Conditional Access signal.

---

## Why These Matter

Many published GSA designs stop at “enable the profile + Conditional Access.” The controls above address the gaps that appear once real users, real networks, and real offboarding events are involved:

- Silent protocol bypasses
- IPv6 path leakage
- Authentication lockout risk
- Lifecycle race conditions
- Limited local diagnostics

Documenting that these were deliberately handled strengthens the credibility of the overall design.

# Conditional Access Design

Conditional Access was the primary enforcement engine once Security Defaults were disabled.

---

## Core Policy Signals

The main access policy required **all** of the following:

| Signal | Requirement | Purpose |
|--------|-------------|---------|
| Device compliance | Device must be marked compliant in Intune | Ensures minimum security baseline |
| Network | Connection must come via Global Secure Access (verified network) | Ensures traffic is brokered through the secure edge |
| MFA | Microsoft Authenticator satisfied | Strong authentication |
| Named locations | Applied where relevant | Additional context-aware control |

If any required signal was missing, access to protected cloud resources was blocked.

---

## Protected Resources

Policies targeted Microsoft 365 cloud resources.  

Legacy authentication was **not** blocked at the time of implementation because certain SharePoint access paths still depended on it. This was recorded as a known limitation and a candidate for future hardening.

---

## Threat Response Policy

A separate policy handled high-risk device signals:

- If malware was detected on the device → user is immediately signed out and blocked from accessing resources.
- Subsequent access attempts are denied until the device state is remediated.

---

## Platform Coverage

Policies were applied to both:
- Windows devices (Entra joined)
- macOS devices (Entra registered)

---

## Design Philosophy

Conditional Access was used to create a clear, enforceable boundary:

> “You may access company resources only when you are on a compliant device, connected through Global Secure Access, and have satisfied MFA.”

This kept the control focused on the access path rather than attempting deep control of the personal device itself.

# Offboarding Automation

A reliable leaver process was treated as a first-class requirement, not an afterthought.

---

## Problem

Simply deleting a user identity in Microsoft Entra ID can leave residual state:
- Device objects still present in Intune
- Work profile / account still present on the endpoint
- Potential residual tokens or cached credentials

In a BYOD environment this is particularly undesirable because the personal side of the device should remain untouched while the work side is cleanly removed.

---

## Designed Sequence

A known race condition exists if the Entra ID account is deleted before Intune has delivered and executed the retire command — the management channel required for the wipe can be lost.

The automation was therefore built to enforce this strict order:

1. **Retire the device** in Microsoft Intune  
   - Triggers removal of the work profile / company data  
   - Revokes relevant management state

2. **Then delete the Entra identity**

This ordering was treated as a hard requirement, not a best-effort step.

---

## Benefits

- Cleaner separation of personal and work identities on the device
- Reduced risk of residual access
- More predictable offboarding experience
- Aligns with the original design goal of preserving personal ownership while still controlling company access

---

## Operational Note

The automation depends on the device still being reachable / checking in during the retirement step. In practice this worked reliably for the majority of cases; edge cases (device offline for extended periods) were handled through standard Intune retirement and cleanup processes.

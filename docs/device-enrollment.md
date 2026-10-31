# Device Enrollment

Enrollment was deliberately **user-driven** to respect the BYOD model. No device was forcibly enrolled by the organisation.

---

## Windows Enrollment

### Prerequisites
- Windows 10 Pro or higher (Windows 10 Home is not supported for this design)
- User must have local administrator rights on the device (required for enrollment)

### Process
1. User goes to **Settings → Accounts → Access work or school**
2. Selects **Connect** / **Join this device to Microsoft Entra ID**
3. Authenticates with their work account
4. Device is joined to Entra ID
5. A separate work account/profile is created on the device

### Outcomes of successful enrollment
- Device appears in Intune and Entra ID
- Microsoft 365 apps can be deployed automatically
- Microsoft Defender can be activated
- Device becomes eligible for Conditional Access compliance evaluation
- Global Secure Access can be applied

### Important note
A full **Entra ID join** (not just registration) was required for Global Secure Access to function correctly on Windows.

---

## macOS Enrollment

### Prerequisites
- Supported macOS version (organisation-specific minimum version applied)
- Apple Push Notification service (APNs) certificate obtained from the Apple Developer / Apple Business account and uploaded to the tenant
- Configuration profiles and required extensions enabled

### Process
1. APNs certificate is configured at the tenant level (one-time setup)
2. User initiates enrollment (Company Portal or equivalent flow)
3. Device is **registered** (not fully joined) to Entra ID
4. Required configuration profiles and extensions are applied

### Key difference from Windows
macOS uses **device registration** rather than a full Entra join. This still provided sufficient signal for Conditional Access and GSA in this implementation, but required more upfront certificate and profile work.

---

## Design Intent

The enrollment model was intentionally lightweight:
- User initiates the process
- A distinct work identity/profile is created
- Personal account and data remain separate
- When the employee leaves, the work profile can be cleanly removed without touching personal content

This separation was one of the primary reasons the design remained acceptable in a strong ownership culture.

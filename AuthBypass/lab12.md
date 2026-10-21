# Lab 12: Authentication Bypass Leads to Complete Account Takeover via Response Manipulation

## Affected URL

[https://mtnbusiness.pwnedbox.site/](https://mtnbusiness.pwnedbox.site/)

## Description

During the security assessment of the MTN Business web application, the authentication mechanism was tested for vulnerabilities that could allow one user to access another user's account by manipulating the authentication response.

The assessment was performed using two provided test accounts: an attacker account and a victim account. The login process was initiated using the attacker account, and the corresponding authentication request and response were intercepted using Burp Suite.

During testing, the authentication response was manipulated by changing the success indicator to `true` and modifying the user email value to the victim's email address. After the modified response was forwarded, the application granted access to the victim's account rather than restricting access to the authenticated attacker account.

This behavior indicates that the application may rely on client-side response data to determine the authentication state and account identity. If the server does not independently validate the authenticated user's identity and enforce session ownership, an attacker could potentially impersonate another user by modifying the response returned to the client.

The observed ability to access the victim's account demonstrates a potential account takeover vulnerability with significant implications for account confidentiality and integrity.

## Steps to Reproduce

1. **Access the application:** Navigate to [https://mtnbusiness.pwnedbox.site/](https://mtnbusiness.pwnedbox.site/).

2. **Use the attacker test account:** Log in using the provided attacker account credentials.

3. **Intercept the authentication transaction:** Configure Burp Suite Proxy to capture the relevant authentication request and response.

4. **Examine the original response:** Identify the response fields that indicate authentication success and the identity of the authenticated user.

5. **Manipulate the response:** In the authorized lab environment, modify the relevant success indicator to `true` and change the user email value from the attacker's email address to the victim's email address.

6. **Forward the modified response:** Forward the response to the application and observe how the client handles the modified authentication information.

7. **Verify account access:** Confirm whether the application displays the victim's account information or grants access to functionality associated with the victim account.

8. **Assess the impact:** Within the lab environment, determine whether the resulting access is limited to a client-side display change or whether authenticated operations are actually performed under the victim's identity.

9. **Capture evidence:** Save screenshots of the original authentication response, the modified response, and the resulting victim account interface.

## POC

Include the following screenshots as evidence:

- **Screenshot 1:** MTN Business login page.
  <img width="871" height="1031" alt="Screenshot 2026-10-21 233909" src="https://github.com/user-attachments/assets/46305895-4500-4edd-87e5-099136f2eed1" />

- **Screenshot 2:** Login initiated using the attacker test account.
  <img width="871" height="1031" alt="Screenshot 2026-10-21 233909" src="https://github.com/user-attachments/assets/d03d8552-c31e-4abf-997e-deb5bd93d617" />

- **Screenshot 3:** Original authentication response showing the attacker account identity.
  <img width="1576" height="819" alt="Screenshot 2026-10-21 233926" src="https://github.com/user-attachments/assets/7546a489-2e54-4372-93cd-e3906a41a933" />

- **Screenshot 4:** Modified response with the success indicator set to `true` and the email value changed to the victim account's email address.
  <img width="1557" height="809" alt="Screenshot 2026-10-21 234059" src="https://github.com/user-attachments/assets/37cada25-d2a7-45c2-8b30-74dd49d4db1c" />

- **Screenshot 5:** Burpsuite displaying the victim's account after the modified response is forwarded.
  <img width="1585" height="807" alt="Screenshot 2026-10-21 234138" src="https://github.com/user-attachments/assets/19270eb3-3f1b-4daa-99f5-f1db51cfb525" />

- **Screenshot 6:** Application using victim account.
  <img width="941" height="793" alt="Screenshot 2026-10-21 234311" src="https://github.com/user-attachments/assets/86a822a4-d6e6-459c-9bdc-91c6269f2579" />


**Test accounts used in the authorized lab:**

- Attacker account: `attacker@mtnbusiness.com`
- Victim account: `victim@mtnbusiness.com`

**Observed result:** Modifying the authentication response to indicate success and identify the victim account resulted in access to the victim account interface.

The passwords are omitted from this report because they are not necessary to document the vulnerability.

**Validation note:** Access to the victim's interface establishes a client-visible identity manipulation issue. To confirm complete account takeover, verify whether the backend accepts sensitive actions under the victim's identity and whether the application establishes or reuses an authenticated session belonging to the victim.

## Impact

If the backend does not independently enforce authentication and account ownership, this vulnerability could result in the following impacts:

1. **Complete account takeover:** An attacker may gain unauthorized access to another user's account without knowing the victim's password.
2. **Unauthorized access to personal information:** Account-specific information and other data available to the victim may be exposed.
3. **Identity impersonation:** An attacker may be able to perform actions while appearing to be the victim.
4. **Unauthorized account modifications:** If protected operations are insufficiently authorized, an attacker may modify account settings or other victim-owned information.
5. **Confidentiality compromise:** Private account data may be accessible to unauthorized users.
6. **Integrity compromise:** Unauthorized actions performed under the victim's identity may alter account information or application records.
7. **Potential privilege abuse:** If affected accounts have elevated permissions, the consequences may extend to additional application functionality.

The actual impact depends on the sensitive data and actions accessible through the victim account and whether the backend independently enforces identity and authorization.

## Remediation

1. **Enforce authentication server-side:** Verify credentials on the server and establish the authenticated identity exclusively from the validated authentication process.
2. **Do not trust client-controlled identity fields:** Never use an email address or user identifier supplied in a client-visible response as proof of the authenticated user's identity.
3. **Use secure server-side sessions:** Associate each session with the authenticated user on the server and validate that association on every protected request.
4. **Enforce authorization on every endpoint:** Ensure that users can access or modify only resources they are authorized to use.
5. **Prevent account identity substitution:** Changing a client-side email field or authentication success indicator must not change the identity associated with the server-side session.
6. **Implement secure session lifecycle controls:** Regenerate session identifiers after authentication and invalidate sessions appropriately during logout or security-sensitive account changes.
7. **Protect sensitive account operations:** Require server-side identity and permission checks for account modifications and other sensitive actions.
8. **Retest authentication and authorization:** Confirm that manipulating client-visible responses cannot grant access to another user's account or permit actions under another user's identity.

## Conclusion

The assessment identified an account takeover condition in the MTN Business web application at [https://mtnbusiness.pwnedbox.site/](https://mtnbusiness.pwnedbox.site/).

During testing, the authentication response was modified to set the success indicator to `true` and replace the attacker account's email address with the victim account's email address. The application subsequently displayed the victim's account interface.

This behavior indicates that the application's client-side authentication state or account identity can be influenced through response manipulation. If the backend also accepts protected requests under the victim's identity, the issue constitutes a **confirmed authentication bypass leading to account takeover**.

The recommended priority is to enforce authentication and account ownership server-side, maintain a trusted association between sessions and user identities, and ensure that every protected operation independently validates authorization.

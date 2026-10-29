## Title

Email Verification Bypass in Customer Portal Allows Account Access Using Arbitrary Verification Codes.

## Affected URL

https://mattermost.pwnedbox.site/

## Description

A business logic vulnerability was identified in the customer portal's email verification workflow during user registration. The application sends a 6-digit one-time passcode (OTP) to verify the user's email address, but fails to properly validate or bind the submitted OTP code to the intended user session or recipient email address on the backend. 

An attacker can bypass the email verification control entirely by submitting an arbitrary or mismatched OTP code (such as a code generated for a different account or session), tricking the server into validating the session and granting full access to the customer portal without proving ownership of the registered email address.

## Steps to Reproduce

1. Navigate to the target application: `https://mattermost.pwnedbox.site/`.
2. Initiate a new user registration with an email address.
3. Observe the email verification prompt requesting the 6-digit verification code.
4. Input an arbitrary or mismatched verification code (or a code obtained from a different transaction/session) into the verification input fields.
5. Click **Verify & Continue** to submit the code.
6. Observe that the application incorrectly accepts the arbitrary code, bypasses email validation, and successfully redirects to the authenticated customer portal dashboard.

## POC

<img width="1180" height="756" alt="image" src="https://github.com/user-attachments/assets/051e2350-8fac-4e2a-bab7-6aa6fe52a27c" />
<img width="1139" height="976" alt="image" src="https://github.com/user-attachments/assets/de3f2a9f-ef05-4c46-bdd8-cbcb8fd8c9a2" />
<img width="1175" height="721" alt="image" src="https://github.com/user-attachments/assets/c98d54b2-e54a-4507-aebd-7636949a29a7" />


## Impact

1. Complete bypass of email verification controls during the registration workflow.
2. Ability for malicious users to register accounts using arbitrary or unverified email addresses.
3. Weakening of account ownership verification and user registration integrity.

## Remediation

1. Implement strict server-side validation to ensure that submitted verification codes are cryptographically bound to the specific user ID, session context, and email address they were generated for.
2. Invalidate verification codes immediately after a single use or upon expiration.
3. Ensure that the backend rejects any mismatch between the active session's expected OTP token and the token provided in the verification request.

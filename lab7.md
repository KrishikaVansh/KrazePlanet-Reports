# Lab 7: OTP Verification Bypass via Response Manipulation

## Affected URL

[https://verify.pwnedbox.site/](https://verify.pwnedbox.site/)

## Description

During the assessment of the target application, the OTP verification mechanism was tested to determine whether verification could be bypassed through HTTP response manipulation using Burp Suite.

The testing involved submitting valid and invalid OTPs, intercepting the corresponding HTTP responses, and comparing the application's behavior. The objective was to determine whether manipulating the response associated with an invalid OTP could cause the client-side application to treat the verification process as successful.

If the application proceeds to grant access to protected functionality without valid OTP verification, this may indicate an OTP verification bypass resulting from improper reliance on client-side response handling.

## Steps to Reproduce

1. **Test the normal authentication flow:** Navigate to [https://verify.pwnedbox.site/](https://verify.pwnedbox.site/) and initiate the OTP verification process using the provided test credentials.

2. **Capture the successful verification response:** Using Burp Suite Proxy, intercept a verification request containing a valid OTP. Forward the request and save the corresponding successful response for comparison.

3. **Capture the unsuccessful verification response:** Repeat the process using an invalid OTP. Intercept the request and examine the server response to identify how the application handles failed verification.

4. **Compare the responses:** Review the successful and unsuccessful responses in Burp Suite. Identify differences in HTTP status codes, response bodies, and verification indicators.

5. **Test response manipulation:** In the authorized lab environment, modify the intercepted response associated with the invalid OTP and observe whether the application's verification state changes.

6. **Verify the security impact:** If the application proceeds despite the invalid OTP, determine whether this allows access to a protected page, completion of test-user registration, or creation of an authenticated session.

7. **Document the results:** Capture screenshots of the original response, the manipulated response, and the resulting application state to demonstrate whether the behavior is reproducible.

## POC

- Screenshot 1: Invalid OTP submission and its corresponding response.
  <img width="1515" height="1109" alt="Screenshot 2026-10-21 225833" src="https://github.com/user-attachments/assets/87b468c2-4ff3-47b9-b972-900bf815a2c7" />
  <img width="1559" height="708" alt="Screenshot 2026-10-21 225851" src="https://github.com/user-attachments/assets/3f321d45-b207-4145-b826-cf068fe253bc" />
- Screenshot 2: Response manipulation performed using Burp Suite.
  <img width="1561" height="757" alt="Screenshot 2026-10-21 225903" src="https://github.com/user-attachments/assets/2c6957c0-1644-4d6a-88d8-13bd6f5d043b" />
- Screenshot 3: Evidence of successful verification or access to protected functionality, if confirmed.
- <img width="1674" height="449" alt="Screenshot 2026-10-21 225912" src="https://github.com/user-attachments/assets/16bfabe3-d3d9-48e4-8855-1757beef329b" />


## Impact

If the vulnerability is confirmed, potential impacts include:

1. **OTP verification bypass:** An attacker may circumvent the intended OTP verification step.
2. **Unauthorized account registration:** An attacker may complete registration without verifying control of the required email address or phone number.
3. **Unauthorized authentication:** If OTP verification is part of the login process, an attacker may gain access without completing the required verification.
4. **Circumvention of security controls:** Improper reliance on client-side response handling may allow the verification interface to enter an invalid state.
5. **Account security risks:** If the bypass grants access to protected accounts or functionality, the confidentiality and integrity of affected accounts may be compromised.

The actual impact depends on whether the server independently validates OTPs and enforces the required authentication state.

## Remediation

1. **Enforce server-side OTP validation:** The server must independently verify the submitted OTP before accepting authentication or registration.
2. **Avoid trusting client-side responses:** Authentication and authorization decisions must not depend solely on client-side interpretation of HTTP responses.
3. **Implement secure session management:** Create or elevate authenticated sessions only after successful server-side verification.
4. **Enforce OTP expiration and single-use restrictions:** OTPs should expire appropriately and become invalid after successful use.
5. **Implement rate limiting:** Restrict repeated OTP verification attempts to reduce brute-force risks.
6. **Validate protected actions server-side:** Ensure every sensitive endpoint independently checks the user's authentication and verification state.
7. **Retest the verification flow:** Confirm that response manipulation cannot bypass server-side OTP verification or grant access to protected functionality.

## Conclusion

The assessment examined whether OTP verification at [https://verify.pwnedbox.site/](https://verify.pwnedbox.site/) could be bypassed through HTTP response manipulation using Burp Suite. The vulnerability should be classified as a confirmed **OTP Verification Bypass via Response Manipulation** only if testing demonstrates that an invalid OTP can lead to successful verification or unauthorized access. Otherwise, the behavior should be documented as an observation requiring further validation.

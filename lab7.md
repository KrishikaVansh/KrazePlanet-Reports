# Lab 7: OTP Verification Bypass via Response Manipulation

## Affected URL

[\[[Insert the target URL](https://verify.pwnedbox.site/)\]]

## Description

During the assessment, the OTP verification mechanism was tested to determine whether authentication could be bypassed by manipulating the server response intercepted through Burp Suite.

The testing involved comparing the application's behavior when invalid OTPs were submitted. The corresponding HTTP responses were captured and examined to determine whether the client-side application relied on the response content to decide whether verification had succeeded.

The assessment methodology focused on determining whether a response associated with an invalid OTP could be manipulated to resemble a successful verification response, potentially causing the application to proceed without valid OTP verification.

If successful verification can be bypassed in this manner and the server subsequently grants access to protected functionality, this may indicate an authentication bypass caused by improper reliance on client-side response handling.

## Steps to Reproduce

1. **Test the normal authentication flow:** Navigate to the target application and initiate the OTP verification process using the provided test credentials.

2. **Capture a successful verification response:** Using Burp Suite Proxy, intercept a verification request containing a valid OTP. Forward the request and save the corresponding successful response for comparison.

3. **Capture an unsuccessful verification response:** Repeat the process using an invalid OTP. Intercept the request and examine the server response to identify how the application handles failed verification.

4. **Compare the responses:** Review the successful and unsuccessful responses in Burp Suite. Identify the differences in response status, body content, and any verification indicators used by the client.

5. **Test response manipulation:** In the authorized lab environment, modify the intercepted response associated with the invalid OTP to determine whether changing the response affects the application's verification state. Record the resulting application behavior.

6. **Verify the security impact:** If the application proceeds despite the invalid OTP, test whether the resulting state grants access to a protected page or permits a security-relevant action, such as completing test-user registration or accessing an authenticated session.

7. **Document the result:** Capture screenshots of the original responses, the manipulation, and the final application state to establish whether the verification bypass is reproducible.

## POC

- Screenshot 1: Invalid OTP submission and its corresponding response.
- Screenshot 2: Response manipulation performed during testing.
- Screenshot 3: Resulting application state after submitting the manipulated response.
- Screenshot 4: Evidence of access to protected functionality, if successfully obtained.


## Impact

If the vulnerability is confirmed, potential impacts include:

1. **OTP verification bypass:** An attacker may circumvent the intended OTP verification step.
2. **Unauthorized account registration:** If OTP verification protects registration, an attacker may be able to complete registration without proving control of the required email address or phone number.
3. **Unauthorized authentication:** If OTP verification is part of the login process, an attacker may gain access to an account without completing the required verification step, depending on the application's authentication design.
4. **Circumvention of security controls:** Reliance on client-side response handling may allow the verification interface to enter an invalid state.
5. **Account security risks:** If the bypass enables access to protected accounts or functionality, the confidentiality and integrity of affected accounts may be compromised.

The actual impact depends on whether the server independently validates OTPs and enforces the required authentication state.

## Remediation

1. **Enforce server-side OTP validation:** The server must independently verify the submitted OTP before accepting authentication or registration.
2. **Do not trust client-side response handling:** Authentication and authorization decisions must not depend solely on client-side interpretation of HTTP responses.
3. **Use secure session management:** Create or elevate authenticated sessions only after successful server-side verification.
4. **Enforce OTP expiration and single-use restrictions:** OTPs should expire appropriately and become invalid after successful use.
5. **Implement rate limiting:** Restrict repeated OTP verification attempts to reduce brute-force risks.
6. **Validate protected actions server-side:** Ensure every sensitive endpoint checks the user's authentication and verification state independently.
7. **Retest the verification flow:** Confirm that modifying client-visible responses cannot bypass server-side OTP verification or grant access to protected functionality.

## Conclusion

The assessment examined whether OTP verification could be bypassed through HTTP response manipulation using Burp Suite. The finding should be classified as a confirmed **OTP Verification Bypass via Response Manipulation** only if testing demonstrates that an invalid OTP can lead to successful verification or unauthorized access. Otherwise, the behavior should be documented as an observation requiring further validation.

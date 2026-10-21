# Lab 11: Email Verification Code Bypass via Response Manipulation During Account Registration

## Affected URL

[https://hackers.pwnedbox.site/](https://hackers.pwnedbox.site/)

## Description

During the security assessment of the UPchieve web application, the account registration process was examined to determine whether email verification could be bypassed through HTTP response manipulation.

The registration workflow consists of multiple steps, beginning with the selection of an account type and followed by the submission of registration details, including an email address and password. The application indicates that a verification code is sent to the registered email address as part of the account creation process.

During testing, the verification process was initiated using the registration workflow, and the relevant HTTP requests and responses were intercepted using Burp Suite. An incorrect verification code was submitted to observe how the application handled unsuccessful verification attempts.

The intercepted response was then modified to indicate successful verification by changing the relevant success indicator to `true`. After the modified response was forwarded, the application proceeded past the verification stage, indicating that the client-side registration workflow could be influenced by manipulating the verification response.

This behavior suggests that the application may rely on client-side response handling to determine whether verification has succeeded. If the server does not independently enforce email verification before creating an account or granting access, this could allow users to bypass the intended verification requirement.

## Steps to Reproduce

1. **Access the registration page:** Navigate to [https://hackers.pwnedbox.site/](https://hackers.pwnedbox.site/).

2. **Initiate account registration:** Select the appropriate registration option and proceed through the initial registration step.

3. **Enter registration details:** Provide the required email address and password, then continue to the verification stage.

4. **Intercept the verification request:** Configure Burp Suite Proxy to capture the HTTP request associated with the verification process.

5. **Submit an incorrect verification code:** Enter an invalid verification code and submit it while monitoring the corresponding HTTP response in Burp Suite.

6. **Examine the response:** Record the original response and identify the field or indicator used by the application to represent verification success or failure.

7. **Manipulate the response:** In the authorized lab environment, modify the relevant success indicator in the intercepted response to indicate success (`true`).

8. **Forward the modified response:** Forward the modified response and observe whether the application proceeds beyond the verification step despite the incorrect code.

9. **Verify the registration outcome:** Determine whether the application permits completion of account registration or grants access to authenticated functionality without successful server-side verification.

10. **Capture supporting evidence:** Save screenshots of the registration workflow, the original unsuccessful verification response, the modified response, and the resulting application state.

## POC

The following screenshots should be included as supporting evidence:

- **Screenshot 1:** Initial registration page displaying the account-type selection.
  <img width="944" height="1022" alt="Screenshot 2026-10-21 232204" src="https://github.com/user-attachments/assets/c5122110-b139-45cf-86e9-b3e5edc6849c" />

- **Screenshot 2:** Registration details page showing the email address and password fields.
  <img width="922" height="1095" alt="Screenshot 2026-10-21 232222" src="https://github.com/user-attachments/assets/4235c606-4d4c-49bc-a888-1297b409e279" />
  
- **Screenshot 3:** Registration details page showing the phone number.
  <img width="923" height="1039" alt="Screenshot 2026-10-21 232330" src="https://github.com/user-attachments/assets/f71ac483-3a78-4886-b705-ff13748c618b" />
  
- **Screenshot 4:** Wrong OTP entered.
  <img width="925" height="1001" alt="Screenshot 2026-10-21 232351" src="https://github.com/user-attachments/assets/85d83e9f-9854-4562-aacb-8ef88dcbdf8e" />

- **Screenshot 5:** Modified verification response showing the success indicator changed to `true`.
  <img width="1566" height="795" alt="Screenshot 2026-10-21 232417" src="https://github.com/user-attachments/assets/cd64115f-9833-428f-9192-658bbdcbb73d" />

- **Screenshot 6:** Application state after forwarding the modified response.
  <img width="934" height="989" alt="image" src="https://github.com/user-attachments/assets/02b899e3-c537-4e36-8856-fbec9ed8ec17" />


**Observed result:** According to the testing procedure, modifying the verification response to indicate success allowed the registration workflow to proceed beyond the verification stage.

**Validation note:** Proceeding to the next registration step does not, by itself, prove that an account was successfully created or that email ownership verification was bypassed server-side. Those outcomes should be verified separately.

## Impact

If the application fails to enforce email verification on the server, the potential impacts include:

1. **Email verification bypass:** An attacker may be able to proceed through registration without supplying a valid verification code.
2. **Unauthorized account registration:** Accounts may be created using email addresses that the registrant has not demonstrated control over.
3. **Abuse of registration functionality:** Attackers may register accounts with unverified or fabricated email addresses, depending on the application's validation controls.
4. **Circumvention of identity verification controls:** If email verification is relied upon to establish account ownership, bypassing it weakens that security control.
5. **Potential unauthorized access:** If the application grants authenticated access before completing server-side verification, protected functionality may become accessible without satisfying the intended registration requirements.
6. **Downstream account security risks:** Other security controls that depend on verified email ownership, such as account recovery or notifications, may be undermined if they treat unverified accounts as verified.

The actual severity depends on whether the server independently validates verification codes, records verification status, and prevents unverified accounts from accessing protected functionality.

## Remediation

1. **Enforce verification server-side:** Validate the verification code on the server before marking an email address as verified.
2. **Do not trust client-side response manipulation:** The application must not treat a client-visible success indicator as authoritative proof of successful verification.
3. **Enforce registration state transitions:** Prevent account creation or activation until the required verification process has been successfully completed on the server.
4. **Bind verification codes to registration attempts:** Associate each code with the intended email address and registration session.
5. **Implement expiration and single-use controls:** Verification codes should expire after a defined period and become invalid after successful use.
6. **Apply rate limiting:** Limit repeated verification attempts and code resends to reduce brute-force and abuse risks.
7. **Validate protected functionality independently:** Ensure authenticated endpoints check account status and required verification flags on every relevant request.
8. **Retest the registration workflow:** Confirm that an incorrect code is rejected even when client-visible responses are manipulated, and that unverified accounts cannot be activated through response modification.

## Conclusion

The assessment identified a potential email verification bypass in the UPchieve registration workflow at [https://hackers.pwnedbox.site/](https://hackers.pwnedbox.site/). During testing, the response associated with an incorrect verification code was manipulated to indicate success, after which the application proceeded beyond the verification stage.

This behavior suggests that the client-side registration flow may rely on a response success indicator when determining whether to continue. Further validation is required to establish whether the server also accepts the unverified registration, creates an active account, or grants access to protected functionality.

The finding can be classified as a confirmed **Email Verification Bypass via Response Manipulation** if testing demonstrates that an invalid verification code results in an account being registered or activated without the required server-side verification.

# Lab 8: Phone OTP Bypass via Response Manipulation

## Affected URL

[https://mobile.pwnedbox.site/](https://mobile.pwnedbox.site/)

## Description

During the security assessment of the mobile application, the phone number OTP verification mechanism was tested for potential authentication bypass through HTTP response manipulation using Burp Suite.

The application implements a three-step workflow consisting of entering a mobile number, verifying the OTP, and accessing the orders functionality. During testing, an invalid OTP was submitted, and the corresponding HTTP request and response were intercepted using Burp Suite Proxy.

The server returned an HTTP `200 OK` response with the following JSON body:

```json
{
  "verify": false,
  "message": "Incorrect OTP. Please try again."
}
```

The response was then manipulated in the authorized lab environment to change the verification indicator from `false` to `true`. Following this manipulation, the application proceeded to the authenticated shopping interface, where product listings and the order-placement workflow were accessible.

Further testing demonstrated that the application allowed the user to proceed to the delivery address selection interface and attempt to place an order after the verification bypass.

This behavior indicates that the application may rely on client-side handling of the OTP verification response without adequately enforcing the verification state before allowing access to protected functionality. The observed behavior represents a potential OTP verification bypass with consequences extending beyond the verification screen.

## Steps to Reproduce

1. **Access the application:** Navigate to [https://mobile.pwnedbox.site/](https://mobile.pwnedbox.site/).

2. **Enter the mobile number:** Use one of the provided lab test accounts and initiate the OTP verification process by selecting the option to send an OTP.

3. **Capture the verification request:** Open Burp Suite Proxy and intercept the OTP verification request. Submit an invalid OTP and inspect the corresponding server response.

4. **Observe the unsuccessful response:** The application returns an HTTP `200 OK` response containing a verification failure indicator:

   ```json
   {
     "verify": false,
     "message": "Incorrect OTP. Please try again."
   }
   ```

5. **Manipulate the response:** In Burp Suite, modify the intercepted response in the authorized lab environment so that the verification indicator is changed from `false` to `true`. Forward the modified response to the client.

6. **Verify the application state:** Observe that the application proceeds to the shopping interface despite the invalid OTP submission.

7. **Test order functionality:** Select a product using the available order-placement functionality. Observe that the application opens the delivery address selection interface.

8. **Document the impact:** Capture evidence of the manipulated response, access to the shopping interface, and availability of the order-placement workflow. If an order is successfully submitted, document the resulting order confirmation separately.

## POC

The following screenshots provide evidence of the observed behavior:

- **Screenshot 1:** Mobile number entry page showing the available test accounts.
  <img width="643" height="796" alt="Screenshot 2026-10-21 230932" src="https://github.com/user-attachments/assets/1a57c23e-41aa-44e1-a969-b476cb8ea215" />

- **Screenshot 2:** OTP verification page displaying the OTP entry interface.
  <img width="653" height="843" alt="Screenshot 2026-10-21 230957" src="https://github.com/user-attachments/assets/922f5648-71ae-4228-b01a-d7f46aa6a70a" />

- **Screenshot 3:** Burp Suite response to an invalid OTP, showing `"verify": false` and the incorrect OTP message.
  <img width="1574" height="701" alt="Screenshot 2026-10-21 231020" src="https://github.com/user-attachments/assets/7f11255b-aab2-4267-9370-cbf3c965df4f" />

- **Screenshot 4:** Burp Suite response after manipulation, showing `"verify": true`.
  <img width="1533" height="821" alt="Screenshot 2026-10-21 231034" src="https://github.com/user-attachments/assets/385c044c-fab6-40cb-aa0d-6218bebda6e1" />

- **Screenshot 5:** Shopping interface accessible after the response manipulation.
  <img width="884" height="967" alt="Screenshot 2026-10-21 231106" src="https://github.com/user-attachments/assets/5f61dc46-dcb4-4aee-a36e-9a39aa4046b2" />


**Observed result:** The application proceeded beyond OTP verification after the client received a manipulated success response. Product browsing and the delivery address selection workflow were accessible.

**Verification note:** The evidence demonstrates that the client-side workflow can be advanced through response manipulation. Confirming whether the server independently enforces OTP verification for order submission requires validating the final order request and its server-side outcome.

## Impact

If the behavior is confirmed to bypass server-side verification controls, the potential impacts include:

1. **Phone OTP verification bypass:** An attacker may circumvent the intended phone number verification step by manipulating the response processed by the client.
2. **Unauthorized access to application functionality:** The application may allow users to reach shopping and order-related interfaces without completing legitimate OTP verification.
3. **Potential unauthorized order placement:** If the server accepts order submissions without independently verifying the required authentication state, an attacker may be able to place orders without completing phone verification.
4. **Circumvention of account verification controls:** The application may fail to reliably establish that a user controls the phone number associated with the verification process.
5. **Business logic abuse:** If additional server-side controls are missing, the bypass could potentially be used to abuse account creation, order workflows, or other functionality protected by phone verification.

The confirmed impact should be limited to the functionality demonstrated by the available evidence. Successful unauthorized order submission has not been established solely by reaching the delivery address selection interface.

## Remediation

1. **Enforce server-side OTP verification:** Validate the OTP on the server and maintain a server-side verification state. Do not rely on a client-visible response to establish successful verification.
2. **Protect sensitive endpoints:** Require valid authentication and the appropriate verification state for order creation and other protected operations.
3. **Use secure session management:** Issue or update authenticated session tokens only after successful server-side OTP validation.
4. **Prevent client-controlled verification state:** Ensure that modifying response content in the browser or intercepting proxy cannot grant access to protected functionality.
5. **Validate order submissions independently:** Verify the user's identity, session state, delivery details, and authorization on the server before processing an order.
6. **Implement OTP expiration and single-use restrictions:** Ensure OTPs expire appropriately and cannot be reused after successful verification.
7. **Apply rate limiting:** Restrict repeated OTP attempts and implement monitoring for suspicious verification activity.
8. **Retest the workflow:** Confirm that invalid OTP submissions remain unsuccessful and that neither response manipulation nor direct requests can bypass server-side verification controls.

## Conclusion

The assessment of `https://mobile.pwnedbox.site/` identified a potential phone OTP verification bypass through HTTP response manipulation. After an invalid OTP submission, the intercepted response was modified to indicate successful verification, and the application proceeded to the shopping interface and delivery address selection workflow.

This demonstrates that the client-side application can be advanced beyond the OTP verification step through response manipulation. Further validation of the server-side order submission process is necessary to determine whether unauthorized orders can actually be placed and to establish the full security impact of the finding.

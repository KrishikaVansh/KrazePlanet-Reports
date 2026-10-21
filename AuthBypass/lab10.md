# Lab 10: Admin Authentication Bypass Leads to Administrative Access via Response Manipulation

## Affected URL

[https://adminpanel.pwnedbox.site/](https://adminpanel.pwnedbox.site/)

## Description

During the security assessment of the UPS Shipment Administration Portal, the administrator login functionality was tested for authentication bypass through HTTP response manipulation.

The login process was tested using the username `admin` and an incorrect password. The authentication request was intercepted using Burp Suite Proxy, and the server's JSON response was examined.

The server returned an HTTP `200 OK` response with the following JSON body:

```json
{
  "status": false,
  "errorMessage": "Username and Password does not match."
}
```

During testing, the response was manipulated by changing the `status` value from `false` to `true`. After the modified response was forwarded, the application displayed the administrative dashboard, including navigation options for Dashboard, Shipments, Reports, Process Return, Users, and Settings.

The dashboard also displayed shipment records, customer-related information, and administrative actions, including a Delete option for shipment records.

This behavior indicates that the application's client-side authentication flow may rely on the response body to determine whether a login attempt succeeds. The ability to reach the administrative interface after manipulating a failed-login response raises concerns about insufficient enforcement of authentication and authorization.

The extent of the vulnerability depends on whether the backend independently verifies the user's authentication state and permissions when administrative functions are accessed.

## Steps to Reproduce

1. **Access the login page:** Navigate to [https://adminpanel.pwnedbox.site/](https://adminpanel.pwnedbox.site/).

2. **Submit incorrect credentials:** Enter the username `admin` and an incorrect password, then click **Sign In**.

3. **Intercept the authentication response:** Configure Burp Suite Proxy to capture the login transaction and inspect the server's response.

4. **Observe the original response:** The server returns an HTTP `200 OK` response containing:

   ```json
   {
     "status": false,
     "errorMessage": "Username and Password does not match."
   }
   ```

5. **Manipulate the response:** In the authorized lab environment, change the `status` value from `false` to `true` in the intercepted response.

6. **Forward the modified response:** Forward the response to the application and observe how the client handles the modified authentication result.

7. **Verify dashboard access:** Observe that the UPS Shipment Administration Portal displays the administrative dashboard and its associated navigation options.

8. **Assess the impact:** Determine whether administrative functions, such as viewing shipment records, accessing customer information, managing users, or performing shipment-related actions, are also accepted by the backend without valid authentication.

9. **Capture evidence:** Save screenshots of the original failed-login response, the modified response, and the resulting administrative dashboard.

## POC

The following screenshots provide evidence of the observed behavior:

- **Screenshot 1:** UPS Shipment Administration Portal login page displaying an authentication failure after incorrect credentials are submitted.
  <img width="925" height="953" alt="Screenshot 2026-10-21 231824" src="https://github.com/user-attachments/assets/eac86482-c7bb-4718-bce4-9d1cd868546a" />

- **Screenshot 2:** Burp Suite showing the login request and original JSON response with `"status": false`.
  <img width="1210" height="530" alt="Screenshot 2026-10-21 231845" src="https://github.com/user-attachments/assets/0292b3bd-61ed-4334-81bd-bef3084024cc" />

- **Screenshot 3:** Burp Suite showing the modified JSON response with `"status": true`.
  <img width="1461" height="629" alt="Screenshot 2026-10-21 231903" src="https://github.com/user-attachments/assets/ee25441c-7316-4826-b759-c2ee9eeb56c0" />

- **Screenshot 4:** Administrative dashboard displayed after the modified response is forwarded, showing shipment reports, customer-related records, and administrative navigation options.
- <img width="934" height="819" alt="Screenshot 2026-10-21 231913" src="https://github.com/user-attachments/assets/c14eb711-6aac-4ccd-9abe-d72a8abd8150" />


**Observed result:** The application displayed the administrative dashboard after the response associated with incorrect credentials was modified to indicate success.

**Validation note:** The available evidence confirms that response manipulation affects the client-visible login state. Access to protected backend operations must be independently verified before concluding that full administrative account takeover is possible.

## Impact

If the backend does not independently enforce authentication and authorization, this vulnerability could result in:

1. **Administrative authentication bypass:** An attacker may be able to reach the administrative interface without valid credentials.
2. **Exposure of shipment information:** Shipment tracking records, pickup and return locations, service details, and processing statuses may be accessible without authorization.
3. **Potential exposure of personally identifiable information (PII):** Customer-related information displayed by the application could be exposed to unauthorized users, depending on the actual records and accessible functionality.
4. **Unauthorized shipment management:** If backend authorization checks are absent, an attacker may be able to modify or delete shipment records.
5. **Unauthorized user administration:** Access to user-management functionality could enable account changes or privilege abuse if the corresponding operations are insufficiently protected.
6. **Operational disruption:** Unauthorized changes to shipment or return records could affect shipment processing and business operations.
7. **Confidentiality and integrity compromise:** Genuine unauthorized access to protected administrative operations could expose sensitive information and allow unauthorized changes to business data.

The overall severity depends on whether the server accepts protected operations without a valid authenticated session. Dashboard visibility alone does not establish that backend authorization has been bypassed.

## Remediation

1. **Enforce authentication server-side:** Validate credentials on the server and reject unsuccessful login attempts independently of client-side response handling.
2. **Do not trust client-controlled response values:** A client-visible `status` field must not serve as the sole basis for granting administrative access.
3. **Implement secure session management:** Create an authenticated session only after successful credential verification.
4. **Enforce authorization on every protected endpoint:** Require valid authentication and appropriate permissions for shipment, report, user, and settings operations.
5. **Protect sensitive customer data:** Apply least-privilege access controls and restrict exposure of customer-related information to authorized personnel.
6. **Secure administrative actions:** Require server-side authorization checks for destructive operations, including shipment deletion and user-management changes.
7. **Log and monitor authentication events:** Record failed logins, administrative access, and sensitive changes to help detect suspicious activity.
8. **Retest the application:** Confirm that incorrect credentials are rejected and that manipulating the client-visible response cannot grant access to the dashboard or protected backend functionality.

## Conclusion

The assessment identified an administrative authentication bypass condition in the UPS Shipment Administration Portal at [https://adminpanel.pwnedbox.site/](https://adminpanel.pwnedbox.site/). After incorrect credentials were submitted, modifying the JSON response from `"status": false` to `"status": true` caused the application to display the administrative dashboard.

This behavior demonstrates that the client-side login flow can be influenced through response manipulation. Further verification is required to determine whether protected backend operations also accept unauthenticated requests. If confirmed, the issue could expose shipment and customer-related information and permit unauthorized administrative actions.


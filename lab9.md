# Lab 9: JSON Response Manipulation Leads to Admin Authentication Bypass

## Affected URL

[https://hub.pwnedbox.site/](https://hub.pwnedbox.site/)

## Description

During the security assessment of the ControlHub Enterprise Management application, the administrator login functionality was tested for authentication bypass through HTTP response manipulation.

The application was tested using the username `admin` and an incorrect password. The authentication request was intercepted using Burp Suite Proxy, and the server's JSON response was examined.

The server returned an HTTP `200 OK` response with the following JSON body:

```json
{
  "status": "0",
  "user_type": ""
}
```

The response indicated an unsuccessful authentication attempt. During testing, the JSON response was manipulated to change the authentication status from `"0"` to `"1"` and the `user_type` value to `"admin"`.

After forwarding the manipulated response, the application granted access to the Enterprise Management Console and displayed an administrator session. The interface showed administrative dashboard information, including user management records, server status information, and system statistics.

This behavior demonstrates that the client-side application can be induced to display and enter an administrative state by manipulating the authentication response. It raises a critical concern that authentication decisions may rely on client-controlled response data rather than being adequately enforced by the server.

Further validation of server-side session creation and authorization is necessary to determine whether the bypass also grants access to protected backend operations.

## Steps to Reproduce

1. **Access the login page:** Navigate to [https://hub.pwnedbox.site/](https://hub.pwnedbox.site/) and open the ControlHub administrator login interface.

2. **Submit incorrect credentials:** Enter the username `admin` and an incorrect password, then click **Sign In**.

3. **Intercept the authentication response:** Use Burp Suite Proxy to intercept the login transaction and inspect the response returned by the server.

4. **Observe the original JSON response:** The server returns an HTTP `200 OK` response with the following body:

   ```json
   {
     "status": "0",
     "user_type": ""
   }
   ```

5. **Manipulate the JSON response:** In the authorized lab environment, modify the response values to indicate successful authentication and an administrator user type:

   ```json
   {
     "status": "1",
     "user_type": "admin"
   }
   ```

6. **Forward the modified response:** Forward the manipulated response to the client and observe how the application handles the authentication result.

7. **Verify the resulting application state:** Observe that the application displays the Enterprise Management Console with an administrator session and dashboard information.

8. **Assess the security impact:** Verify whether protected administrative actions and backend endpoints are accessible, and whether the server independently enforces authentication and authorization.

## POC

The following screenshots document the observed behavior:

- **Screenshot 1:** ControlHub administrator login page displaying an authentication failure after incorrect credentials are submitted.
  <img width="912" height="998" alt="Screenshot 2026-10-21 231432" src="https://github.com/user-attachments/assets/ce201cc7-c4fb-4e05-bcb5-2fdd4c0a7808" />

- **Screenshot 2:** Burp Suite request and original JSON response showing `"status": "0"` and an empty `user_type`.
  <img width="1564" height="643" alt="Screenshot 2026-10-21 231507" src="https://github.com/user-attachments/assets/7a537783-4285-4c5c-82b9-c0bacf4b445c" />

- **Screenshot 3:** Burp Suite response after manipulation, showing `"status": "1"` and `"user_type": "admin"`.
  <img width="1559" height="762" alt="Screenshot 2026-10-21 231519" src="https://github.com/user-attachments/assets/59f34334-3cef-4709-a64d-285578918101" />

- **Screenshot 4:** Enterprise Management Console displayed after the manipulated response is forwarded, showing an administrator session and administrative dashboard information.
  <img width="937" height="1045" alt="Screenshot 2026-10-21 231530" src="https://github.com/user-attachments/assets/4d237471-3da9-4ee3-9c8c-13b6932e12f9" />


**Observed result:** Incorrect credentials were submitted, and the authentication response was manipulated to indicate administrator access. The application subsequently displayed the Enterprise Management Console.

**Validation note:** The screenshots establish that response manipulation changes the client-visible authentication state. Server-side authorization must be tested separately to determine whether this behavior enables genuine administrative access to protected backend functionality.

## Impact

If the server does not independently enforce authentication and authorization, this vulnerability could have the following consequences:

1. **Administrative authentication bypass:** An attacker may be able to reach the administrator interface without supplying valid credentials.
2. **Unauthorized access to management functionality:** Administrative features may become accessible to unauthenticated users if backend authorization checks are missing.
3. **Exposure of sensitive information:** User records, internal server information, system statistics, or other administrative data may be exposed.
4. **Potential unauthorized modification:** If protected administrative operations are also accessible, an attacker may be able to modify user accounts or management settings.
5. **Infrastructure security risks:** Access to server management functionality could expose operational details useful for further attacks.
6. **Loss of confidentiality and integrity:** Genuine unauthorized administrative access could compromise sensitive information and the integrity of the management platform.

The severity depends on whether the server accepts unauthorized administrative requests, rather than merely allowing the client to display the dashboard.

## Remediation

1. **Enforce server-side authentication:** Verify submitted credentials on the server and reject unsuccessful authentication attempts regardless of client-side response handling.
2. **Do not trust client-controlled authentication state:** Treat JSON fields such as `status` and `user_type` as response data, not as proof of authorization.
3. **Implement secure session management:** Issue an authenticated session only after successful server-side credential validation.
4. **Enforce server-side authorization:** Check the authenticated user's permissions on every administrative endpoint and sensitive operation.
5. **Prevent privilege escalation:** Determine the user's role from trusted server-side session data rather than accepting a client-supplied role.
6. **Use appropriate failure responses:** Return consistent authentication failure responses and ensure the client cannot override the server's authorization decision.
7. **Audit administrative access:** Review authentication logs and protected endpoint activity for unexpected access.
8. **Retest the vulnerability:** Confirm that incorrect credentials are rejected and that modifying the client-visible response cannot grant access to administrative functionality or protected backend operations.

## Conclusion

The assessment identified a potential administrator authentication bypass in the ControlHub application at [https://hub.pwnedbox.site/](https://hub.pwnedbox.site/). After an unsuccessful login using incorrect credentials, manipulating the JSON response to indicate administrator authentication caused the application to display the Enterprise Management Console.

The observed behavior demonstrates a client-side authentication state manipulation issue. Additional server-side testing is required to establish whether protected administrative operations can also be performed without valid credentials. If confirmed, the issue should be treated as a high-impact authentication and authorization vulnerability.

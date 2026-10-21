# Lab 13: Authentication Bypass via Response Manipulation Leads to Information Disclosure

## Affected URL

[https://airforce.pwnedbox.site/](https://airforce.pwnedbox.site/)

## Description

During the security assessment of the AirForce web application, the authentication mechanism was examined to determine whether unauthorized access could be obtained through HTTP response manipulation.

The application presented a username and password login interface. During testing, an authentication request was submitted using invalid credentials, and the corresponding HTTP response was intercepted and examined using Burp Suite.

The original response returned an HTTP `401 Unauthorized` status code with the following JSON body:

```json
{
  "errors": [
    {
      "code": "UNAUTHORIZED",
      "message": "UnAuthorize"
    }
  ]
}
```

During the assessment, the response was modified to return an HTTP `200 OK` status code and a JSON body indicating successful authorization:

```json
{
  "success": [
    {
      "code": "AUTHORIZED",
      "message": "Authorize"
    }
  ]
}
```

After the modified response was forwarded, the application permitted access to information related to projects and other application data that was otherwise associated with the authenticated interface.

This behavior indicates that the application's client-side authentication flow may rely on the HTTP status code and response body to determine whether a user is authorized. If the backend does not independently enforce authentication and authorization on protected resources, this weakness could allow unauthorized users to access sensitive application information.

The observed exposure of project-related information raises concerns regarding the confidentiality of data accessible through the application.

## Steps to Reproduce

1. **Access the application:** Navigate to [https://airforce.pwnedbox.site/](https://airforce.pwnedbox.site/).

2. **Examine the login interface:** Observe that the application requires a username and password for authentication.

3. **Submit invalid credentials:** Enter invalid login credentials and submit the authentication request.

4. **Intercept the response:** Use Burp Suite to capture and inspect the corresponding HTTP response.

5. **Observe the original response:** Confirm that the server returns an HTTP `401 Unauthorized` status code with an error response indicating that authorization has failed.

6. **Manipulate the response:** In the authorized lab environment, modify the response status code from `401 Unauthorized` to `200 OK` and replace the error JSON body with the following success-indicating response:

   ```json
   {
     "success": [
       {
         "code": "AUTHORIZED",
         "message": "Authorize"
       }
     ]
   }
   ```

7. **Forward the modified response:** Forward the modified response and observe the application's behavior.

8. **Verify information disclosure:** Examine the resulting application interface and identify the project-related information and other data displayed after the modified response is processed.

9. **Validate backend access controls:** Where authorized, verify whether the corresponding project-data endpoints return sensitive information without a valid authenticated session.

10. **Capture evidence:** Save screenshots of the original login failure, the original and modified HTTP responses, and the project-related information accessible after manipulation.

## POC

Include the following screenshots as evidence:

- **Screenshot 1:** AirForce application login page showing the username and password fields.
  <img width="876" height="907" alt="Screenshot 2026-10-21 233139" src="https://github.com/user-attachments/assets/b7ecf836-6fc0-468c-9d80-cd15afbce928" />

- **Screenshot 2:** Burp Suite showing the original HTTP `401 Unauthorized` response and the JSON error body.
  <img width="1502" height="742" alt="Screenshot 2026-10-21 233152" src="https://github.com/user-attachments/assets/cd10775f-4e4c-4834-940f-baa9153a6597" />

- **Screenshot 3:** Modified HTTP `200 OK` response containing the `AUTHORIZED` success indicator.
  <img width="1583" height="803" alt="Screenshot 2026-10-21 233442" src="https://github.com/user-attachments/assets/ff9253b5-4365-440b-80af-f775168aac92" />

- **Screenshot 4:** Application interface displayed after the modified response is forwarded.
<img width="924" height="1090" alt="Screenshot 2026-10-21 233524" src="https://github.com/user-attachments/assets/03c2b66a-fd9c-4aaa-97f4-d09b84da5311" />


**Observed result:** Following modification of the authentication response, the application displayed project-related information and other application data.

**Validation note:** Response manipulation demonstrates that the client-side application can be influenced. To establish a server-side authentication bypass, confirm that protected data is returned by the backend without valid authentication, rather than being displayed solely because of client-side state changes.

## Impact

If the backend does not independently enforce authentication and authorization, the vulnerability could result in the following impacts:

1. **Authentication bypass:** Unauthorized users may gain access to application functionality intended for authenticated users.
2. **Sensitive information disclosure:** Project-related information and other protected application data may become accessible to unauthorized parties.
3. **Exposure of confidential project details:** Depending on the data available, unauthorized access could reveal project descriptions, internal records, operational information, or other confidential details.
4. **Unauthorized access to protected resources:** If backend access controls are insufficient, an attacker may retrieve information directly from protected endpoints without valid credentials.
5. **Confidentiality compromise:** Exposure of internal project data may affect the confidentiality of organizational information.
6. **Potential follow-on attacks:** Disclosed information could assist further attacks by revealing internal workflows, project details, or other information useful for reconnaissance.
7. **Business and reputational impact:** Unauthorized disclosure of confidential project information may affect operational security and organizational trust.

The actual severity depends on the sensitivity of the exposed information, the scope of accessible resources, and whether the backend independently validates authentication and authorization.

## Remediation

1. **Enforce server-side authentication:** Validate usernames and passwords on the server and establish authenticated sessions only after successful credential verification.
2. **Do not trust client-visible responses:** HTTP status codes and JSON response fields must not be treated as substitutes for server-side authentication.
3. **Enforce authorization on every protected endpoint:** Require a valid authenticated session and appropriate permissions for all project-related resources and sensitive operations.
4. **Protect sensitive project information:** Restrict access to confidential data according to user roles and the principle of least privilege.
5. **Implement secure session management:** Use securely generated session identifiers, appropriate session expiration, and proper invalidation of unauthenticated or expired sessions.
6. **Return appropriate authentication errors:** Ensure failed authentication attempts produce consistent error responses and do not grant access to protected resources.
7. **Review API access controls:** Test project-data endpoints independently to confirm that they reject unauthenticated requests, regardless of client-side response manipulation.
8. **Monitor authentication activity:** Log failed authentication attempts and suspicious requests to protected endpoints.
9. **Retest the application:** Confirm that modifying client-visible responses cannot grant access to protected application data or bypass server-side authorization checks.

## Conclusion

The assessment identified a potential authentication bypass and information disclosure issue in the AirForce web application at [https://airforce.pwnedbox.site/](https://airforce.pwnedbox.site/).

During testing, an authentication failure response containing an HTTP `401 Unauthorized` status code and an error JSON body was modified to return HTTP `200 OK` with a success-indicating JSON response. Following this modification, the application displayed project-related information and other application data.

This behavior suggests that the client-side authentication flow may rely on the response status and body to determine whether access should be granted. If protected information can also be retrieved from the backend without valid authentication, the issue should be classified as a confirmed **Authentication Bypass Leading to Information Disclosure**.

The recommended priority is to enforce authentication and authorization server-side and verify that all protected project-data endpoints reject unauthenticated requests.

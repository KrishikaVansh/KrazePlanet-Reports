## Title

Business Logic Bypass Allows Setting "Read Access" Role Without Pro Plan Subscription on Project Sharing Portal.

## Affected URL

https://lovable.pwnedbox.site/index.php?action=magic-codes&project_id=2

## Description

A business logic and authorization vulnerability was identified in the project sharing and access control configuration workflow. The application restricts setting the public invite link access level to "View access" (Read Access) behind a paid Pro plan subscription. 

However, the backend endpoint responsible for updating access levels (`/index.php?action=magic-codes`) fails to properly validate whether the user possesses an active Pro subscription. An attacker can intercept the configuration request and directly submit JSON parameters requesting `access_level: "read"`, successfully bypassing the UI paywall and enabling "View access" for project invite links without upgrading or paying for a Pro plan.

## Steps to Reproduce

1. Navigate to the target application: `https://lovable.pwnedbox.site/`.
2. Log into a standard (non-Pro) user account.
3. Open an existing project and click on the **Share** menu to view project access settings.
4. Attempt to change the invite link access level to "View access" and observe that it is gated behind a Pro plan requirement.
5. Intercept the HTTP request using a proxy tool (e.g., Burp Suite) when configuring the magic codes or invite access endpoint (`/index.php?action=magic-codes&project_id=2`).
6. Modify the JSON body of the request to explicitly set `{"access_level": "read"}`.
7. Forward the request and observe that the server accepts the configuration change without validating subscription status, successfully enabling Read Access for the project invite link.

## POC

<img width="749" height="774" alt="image" src="https://github.com/user-attachments/assets/4ce8ab45-7505-4104-895d-4dc274affbbb" />
<img width="1178" height="680" alt="image" src="https://github.com/user-attachments/assets/b05643b9-2fc5-4c50-9781-0c4d9647283e" />
<img width="1024" height="600" alt="image" src="https://github.com/user-attachments/assets/6c41f52e-8926-47ec-974f-f16b730bc986" />
<img width="1074" height="844" alt="image" src="https://github.com/user-attachments/assets/456b6dad-45d8-4ba1-b78e-148403fdba36" />
<img width="430" height="385" alt="image" src="https://github.com/user-attachments/assets/9a7952e1-9c2c-470f-a92b-aecdb073aba9" />


## Impact

1. Complete bypass of subscription paywalls and monetization controls governing premium features.
2. Unauthorized configuration of restricted access levels (such as View-only public links) on projects without an active Pro subscription.
3. Integrity violation of feature entitlement checks across user tiers.

## Remediation

1. Implement rigorous server-side subscription validation on all endpoints handling feature configurations like project access levels.
2. Verify that the authenticated user account holds an active Pro plan entitlement before allowing changes to restricted settings such as Read Access invite links.
3. Reject configuration requests for premium access levels if the user's account tier does not match the required subscription level.

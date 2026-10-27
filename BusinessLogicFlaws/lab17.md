## Title

Free Trial Parameter Tampering Allows Escalating to Pro Plan and Extending Trial Duration Arbitrarily.

## Affected URL

https://gigboard.pwnedbox.site/upgrade/trial

## Description

A business logic vulnerability was identified in the subscription upgrade workflow where parameters controlling trial duration (`trial_days`) are accepted directly from the client without proper server-side validation or enforcement of default trial constraints. 

An attacker can intercept the HTTP POST request triggered during the free trial activation and modify parameters such as `trial_days` to a significantly larger value (e.g., setting it to `30` days or more). Consequently, the server provisions the Pro plan and extends the trial expiration period accordingly, allowing unauthorized access to paid platform features without valid payment.

## Steps to Reproduce

1. Navigate to the target application: `https://gigboard.pwnedbox.site/register`.
2. Create a new user account and log into the platform.
3. Navigate to the upgrade/pricing page (`https://gigboard.pwnedbox.site/upgrade`) and click on the free trial activation button while intercepting the request using a proxy tool (e.g., Burp Suite).
4. Locate the `trial_days` parameter within the POST body of the `/upgrade/trial` endpoint.
5. Modify the `trial_days` parameter value from its default length to an arbitrarily extended duration (e.g., change it to `30`).
6. Forward the request and observe that the user account is successfully upgraded to the Pro plan with an extended trial expiration date.

## POC
<img width="1081" height="768" alt="image" src="https://github.com/user-attachments/assets/d404de11-1593-4ac9-b1cf-be643f438535" />
<img width="1090" height="980" alt="image" src="https://github.com/user-attachments/assets/9b20f429-a375-42c7-a333-9c03dfa66741" />
<img width="1085" height="1131" alt="image" src="https://github.com/user-attachments/assets/18833bcc-c258-4a0c-a926-35fc86fb709d" />
<img width="1105" height="691" alt="image" src="https://github.com/user-attachments/assets/df7fd62b-3f0b-4caa-823e-ff8dd4a51267" />



## Impact

1. Unauthorized privilege escalation granting full access to paid Pro features, analytics, and platform tiers without payment.
2. Arbitrary extension of trial subscription durations, bypassing monetization limits.
3. Financial loss and distortion of subscription management logic.

## Remediation

1. Remove trial duration parameters from client-submitted requests entirely; enforce trial periods strictly via fixed server-side configuration constants based on the selected plan.
2. Implement rigorous server-side validation to ensure that trial extension requests match authorized business rules and cannot be arbitrarily manipulated by users.
3. Ensure proper authorization checks are performed before provisioning premium or trial features to user accounts.

## Title

Stored HTML Injection via Unsanitized Form Parameters on HackerOne Pentest Community Notification Platform.

## Affected URL

https://notifications.pwnedbox.site/

## Description

A Stored HTML Injection vulnerability was identified in the application form and notification dispatch module of the HackerOne Pentest Community clone platform. User-supplied input submitted through form parameters (such as first name and last name fields) is stored in the application backend and rendered directly without proper sanitization, validation, or output encoding into generated email notifications and user interfaces.

An attacker can submit arbitrary HTML markup or hyperlink tags into these input parameters. When the application processes the submission and dispatches an automated notification email or renders the summary view, the injected HTML is interpreted natively by the email client or browser, altering the message structure and enabling phishing or spoofing attacks.

## Steps to Reproduce

1. Navigate to the pentest community application form: `https://notifications.pwnedbox.site/`.
2. Input an HTML markup or hyperlink payload into the form fields (such as First Name or Last Name inputs).
3. Complete the required application fields and submit the form.
4. Check the generated notification preview or inbox to observe the processed output.
5. Observe that the injected HTML tags are rendered directly as interactive links or formatted elements within the notification interface.

## POC
<img width="1024" height="794" alt="image" src="https://github.com/user-attachments/assets/eae8541b-d56d-4d16-99d5-4ec606bf1db2" />
<img width="866" height="753" alt="image" src="https://github.com/user-attachments/assets/9b6e8074-832a-4714-ba6c-4eddaf786238" />

## Impact

1. Structural modification and visual defacement of automated notification templates and communication emails.
2. Phishing risks by spoofing legitimate confirmation links, branding components, or URLs to deceive recipients.
3. Delivery of secondary client-side attack vectors if combined with unescaped markup rendering.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all user-supplied form inputs before rendering them in notification templates or web views.
2. Apply comprehensive input validation to reject or strip unexpected special characters and HTML markup tags from form parameters.
3. Utilize safe text rendering methods rather than raw HTML evaluation when generating outbound communications and summaries.

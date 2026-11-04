## Title

Stored Cross-Site Scripting (XSS) via Unsanitized Support Ticket Input Fields on DigitalOcean Portal.

## Affected URL

https://kzlabs.in/subdomains/digitalocean/index.php?view=create

## Description

A Stored Cross-Site Scripting (XSS) vulnerability was identified in the support ticket submission feature. The application accepts user-supplied input through support ticket fields such as the subject and description, storing them in the database without proper server-side sanitization, HTML entity encoding, or output escaping.

When support operators or users view the support tickets list or details page, the stored malicious payload (e.g., JavaScript injected via the ticket creation form) is rendered directly into the Document Object Model (DOM) by the application. Consequently, arbitrary JavaScript executes automatically in the browser of any user viewing the affected support ticket sections.

## Steps to Reproduce

1. Navigate to the target application: `https://kzlabs.in/subdomains/digitalocean/index.php?view=create`.
2. Log into a registered user account on the portal.
3. Locate the **Create Support Ticket** form.
4. Enter a malicious payload containing HTML and JavaScript tags into input fields such as the subject or description (e.g., `krishika11'"><script>confirm(11)</script>`).
5. Click **Submit Support Case** to store the payload in the database.
6. Navigate to the tickets view (`https://kzlabs.in/subdomains/digitalocean/index.php?view=tickets`) or view the ticket details to observe that the script executes immediately, triggering a confirmation or alert box (`11`).

## POC
<img width="1433" height="981" alt="image" src="https://github.com/user-attachments/assets/9627d014-dbdb-4a01-a41e-acd396f54b03" />

<img width="1553" height="633" alt="image" src="https://github.com/user-attachments/assets/fe184741-b1d5-438e-b5cf-72c5dfbaebed" />

## Impact

1. Persistent execution of arbitrary JavaScript code in the browser of any user or administrator viewing the compromised support tickets.
2. Session hijacking, cookie theft, and unauthorized actions performed on behalf of authenticated support staff or users.
3. Defacement of support management panels and widespread compromise of portal users.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all user-generated ticket content before rendering it in the DOM.
2. Sanitize user input on the server side using a robust HTML sanitizer library to strip dangerous tags and event handlers (`onerror`, `script`, etc.) before storing tickets in the database.
3. Deploy a strict Content Security Policy (CSP) to mitigate the impact of potential XSS injections.

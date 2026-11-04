## Title

Stored Cross-Site Scripting (XSS) via Unsanitized Vulnerability Report Input Fields on HackerOne Portal.

## Affected URL

https://kzlabs.in/subdomains/hackerone/index.php?view=submit

## Description

A Stored Cross-Site Scripting (XSS) vulnerability was identified in the vulnerability report submission feature. The application accepts user-supplied input through report fields such as the title, description, and impact, storing them in the database without proper server-side sanitization, HTML entity encoding, or output escaping.

When users or triage analysts view the submitted reports list or report details page, the stored malicious payload (e.g., JavaScript injected via the reporting form) is rendered directly into the Document Object Model (DOM) by the application. Consequently, arbitrary JavaScript executes automatically in the browser of any user viewing the affected vulnerability report sections.

## Steps to Reproduce

1. Navigate to the target application: `https://kzlabs.in/subdomains/hackerone/index.php?view=submit`.
2. Log into a registered user account on the portal.
3. Locate the **Submit Vulnerability Report** form.
4. Enter a malicious payload containing HTML and JavaScript tags into input fields such as the title, description, or impact (e.g., `krishika'"<script>alert(1)</script>`).
5. Click **Submit report** to store the payload in the database.
6. Navigate to the dashboard or report details view (`https://kzlabs.in/subdomains/hackerone/index.php?view=dashboard&report_id=6`) to observe that the script executes immediately, triggering an alert or confirmation box.

## POC
<img width="1637" height="1237" alt="image" src="https://github.com/user-attachments/assets/af5bd885-bab0-4051-9f65-3ab2b66625bd" />

<img width="1568" height="573" alt="image" src="https://github.com/user-attachments/assets/eaa43ca0-a4b8-4bc5-afac-ea534746f896" />


## Impact

1. Persistent execution of arbitrary JavaScript code in the browser of any user or triage security analyst viewing the compromised vulnerability reports.
2. Session hijacking, cookie theft, and unauthorized actions performed on behalf of authenticated portal users.
3. Defacement of security reporting portals and widespread compromise of platform participants.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all user-generated report content before rendering it in the DOM.
2. Sanitize user input on the server side using a robust HTML sanitizer library to strip dangerous tags and event handlers (`onerror`, `script`, etc.) before storing reports in the database.
3. Deploy a strict Content Security Policy (CSP) to mitigate the impact of potential XSS injections.

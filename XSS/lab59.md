## Title

Stored Cross-Site Scripting (XSS) via Unsanitized Event Description Input Field on Events and Polls Portal.

## Affected URL

https://kzlabs.in/subdomains/articles/index.php

## Description

A Stored Cross-Site Scripting (XSS) vulnerability was identified in the community event creation feature. The application accepts user-supplied input through event input fields such as the event description and stores it in the database without proper server-side sanitization, HTML entity encoding, or output escaping.

When users view the scheduled events or portal pages, the stored malicious payload (e.g., JavaScript injected via the event creation form) is rendered directly into the Document Object Model (DOM) by the application. Consequently, arbitrary JavaScript executes automatically in the browser of any user viewing the affected event section.

## Steps to Reproduce

1. Navigate to the target application: `https://kzlabs.in/subdomains/articles/index.php`.
2. Log into a registered user account on the community hub portal.
3. Locate the **Create Community Event** section.
4. Enter event details including a malicious payload containing HTML and JavaScript tags into the event description or title fields.
5. Click **Publish Event** to store the payload in the database.
6. Refresh the page or view the scheduled events list as any user to observe that the script executes immediately, triggering a confirmation or alert box (`1`).

## POC

<img width="1538" height="1054" alt="image" src="https://github.com/user-attachments/assets/fe99e770-921a-4485-8a7d-e476cd6b7fb6" />
<img width="1317" height="498" alt="image" src="https://github.com/user-attachments/assets/8c94f46e-c9db-4888-bc7e-f8a73f7a4b90" />


## Impact

1. Persistent execution of arbitrary JavaScript code in the browser of any user viewing the compromised community events portal.
2. Session hijacking, cookie theft, and unauthorized actions performed on behalf of authenticated visitors.
3. Defacement of event portals and widespread compromise of portal users.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all user-generated event content before rendering it in the DOM.
2. Sanitize user input on the server side using a robust HTML sanitizer library to strip dangerous tags and event handlers (`onerror`, `script`, etc.) before storing events in the database.
3. Deploy a strict Content Security Policy (CSP) to mitigate the impact of potential XSS injections.

## Title

Stored Cross-Site Scripting (XSS) via Unsanitized Product Review Input Field on Members Portal.

## Affected URL

https://kzlabs.in/subdomains/members/index.php?product_id=2

## Description

A Stored Cross-Site Scripting (XSS) vulnerability was identified in the product review submission feature. The application accepts user-supplied input through review text fields and stores it in the database without proper server-side sanitization, HTML entity encoding, or output escaping.

When users view the product review page, the stored malicious payload (e.g., JavaScript injected via the review form) is rendered directly into the Document Object Model (DOM) by the application. Consequently, arbitrary JavaScript executes automatically in the browser of any user viewing the affected product review section.

## Steps to Reproduce

1. Navigate to the target application: `https://kzlabs.in/subdomains/members/index.php?product_id=2`.
2. Log into a registered user account on the members portal.
3. Locate a product review section or submission form.
4. Enter a malicious payload containing HTML and JavaScript tags into the review input field.
5. Submit the review to store the payload in the database.
6. Refresh the page or view the product details as any user to observe that the script executes immediately, triggering an alert box (`22`).

## POC

<img width="1701" height="596" alt="image" src="https://github.com/user-attachments/assets/3f7973d2-6144-4f5f-914a-8ece421f5064" />

## Impact

1. Persistent execution of arbitrary JavaScript code in the browser of any user viewing the compromised product review page.
2. Session hijacking, cookie theft, and unauthorized actions performed on behalf of authenticated visitors.
3. Defacement of product review sections and widespread compromise of portal users.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all user-generated review content before rendering it in the DOM.
2. Sanitize user input on the server side using a robust HTML sanitizer library to strip dangerous tags and event handlers (`onerror`, `script`, etc.) before storing reviews in the database.
3. Deploy a strict Content Security Policy (CSP) to mitigate the impact of potential XSS injections.

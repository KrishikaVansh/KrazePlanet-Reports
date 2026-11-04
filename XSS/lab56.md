## Title

Reflected Cross-Site Scripting (XSS) via Unsanitized URL Path Parameters on Widgets Community Platform.

## Affected URL

https://widgets.pwnedbox.site/index.php/svc/shreddit/api/comments/askreddit/t3_u9po1l/

## Description

A reflected Cross-Site Scripting (XSS) vulnerability was identified in the web application's routing and comment thread path handling. The application takes user-supplied input from the URL path segments and reflects it directly into the Document Object Model (DOM) or page output without proper input sanitization, encoding, or output escaping.

An attacker can craft a malicious URL containing an HTML/JavaScript payload (e.g., `<img src=x onerror=alert(1)>`) injected into the resource path. When a victim user visits the crafted URL, the malicious script executes within the context of their session in the browser, allowing the execution of arbitrary JavaScript code.

## Steps to Reproduce

1. Navigate to the target application: `https://widgets.pwnedbox.site/index.php/svc/shreddit/api/comments/askreddit/t3_u9po1l/t1_i5sxroa`[cite: 45].
2. Identify that parts of the URL path structure are reflected directly into the page response or DOM elements.
3. Craft a malicious URL injecting a script payload into the path parameter, such as: `https://widgets.pwnedbox.site/index.php/svc/shreddit/api/comments/askreddit/t3_u9po1lkrishika'"><img%20src=x%20onerror=alert(1)>/t1_i5sxroa`[cite: 46].
4. Load the crafted URL in a web browser.
5. Observe that the JavaScript payload executes and triggers an alert box (`1`), confirming reflected XSS execution[cite: 46].

## POC

<img width="1291" height="903" alt="image" src="https://github.com/user-attachments/assets/c626b7fa-7671-4d3e-b1c6-eb912b2ffd6a" />

<img width="1524" height="1008" alt="image" src="https://github.com/user-attachments/assets/20f4e4be-d2bc-4655-9755-2577f023d5a2" />

## Impact

1. Execution of arbitrary JavaScript code in the browser of any user who visits the crafted malicious link.
2. Potential session hijacking, cookie theft, or unauthorized actions performed on behalf of the victim user.
3. Defacement or redirection of the affected web application interface.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all user-supplied data reflected in HTTP responses or rendered in the DOM.
2. Sanitize and validate all URL path segments and parameters before reflecting them back to the user interface.
3. Deploy a robust Content Security Policy (CSP) to restrict the execution of inline scripts and unauthorized script sources.

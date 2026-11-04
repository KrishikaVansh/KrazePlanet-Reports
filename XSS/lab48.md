## Title

Reflected Cross-Site Scripting (XSS) via Unsanitized `academy` Parameter in Query String on Settings Platform.

## Affected URL

https://settings.pwnedbox.site/

## Description

A reflected Cross-Site Scripting (XSS) vulnerability was identified in the web application's query parameter handling. The application takes user-supplied input from the query string (specifically via the `academy` parameter) and reflects it directly into the Document Object Model (DOM) or page response without proper input sanitization, encoding, or output escaping.

An attacker can craft a malicious URL containing an obfuscated JavaScript payload (e.g., using Unicode escape sequences to bypass weak keyword filtering) injected into the `academy` parameter. When a victim user visits the crafted URL, the malicious script executes within the context of their session in the browser, allowing the execution of arbitrary JavaScript code.

## Steps to Reproduce

1. Navigate to the target application: `https://settings.pwnedbox.site/`.
2. Identify that user-supplied query parameters (such as `academy`) are reflected directly into the page output or DOM elements.
3. Craft a malicious URL injecting an obfuscated script payload into the parameter, such as: `https://settings.pwnedbox.site/?academy=krishika%27%22%3C%3E%3CSCrIpT%3Ea%5Cu006fcert%281%29%3C%2FSCrIpT%3E`.
4. Load the crafted URL in a web browser.
5. Observe that the JavaScript payload executes and triggers an alert box (`1`), confirming reflected XSS execution via filter bypass.

## POC

<img width="1264" height="476" alt="image" src="https://github.com/user-attachments/assets/b2c00fde-2c7e-450a-9235-1a2c8260ec36" />


## Impact

1. Execution of arbitrary JavaScript code in the browser of any user who visits the crafted malicious link.
2. Potential session hijacking, cookie theft, or unauthorized actions performed on behalf of the victim user.
3. Defacement or redirection of the affected web application interface.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all user-supplied data reflected in HTTP responses or rendered in the DOM.
2. Sanitize and validate all query parameters (`academy`) before reflecting them back to the user interface rather than relying on weak blacklists or keyword filters.
3. Deploy a robust Content Security Policy (CSP) to restrict the execution of inline scripts and unauthorized script sources.

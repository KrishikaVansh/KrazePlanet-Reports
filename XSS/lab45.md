## Title

Reflected Cross-Site Scripting (XSS) via Unsanitized `lname` Parameter in Query String on Tickets Platform.

## Affected URL

https://tickets.pwnedbox.site/

## Description

A reflected Cross-Site Scripting (XSS) vulnerability was identified in the web application's query parameter handling. The application takes user-supplied input from the query string (specifically via the `lname` parameter) and reflects it directly into the Document Object Model (DOM) or page response without proper input sanitization, encoding, or output escaping.

An attacker can craft a malicious URL containing an obfuscated JavaScript payload (e.g., using Unicode escape sequences like `\u0061lert` to bypass rudimentary string filters) injected into the `lname` parameter. When a victim user visits the crafted URL, the malicious script executes within the context of their session in the browser, allowing the execution of arbitrary JavaScript code.

## Steps to Reproduce

1. Navigate to the target application: `https://tickets.pwnedbox.site/`.
2. Identify that user-supplied GET parameters (such as `lname`) are reflected directly into the page output or DOM elements.
3. Craft a malicious URL injecting an obfuscated script payload into the parameter, such as: `https://tickets.pwnedbox.site/?fname=krishika1%27%22%3C%3E&lname=krishika2%27%22%3C%3E%3CscRIpt%3E%5Cu0061lert%281%29%3C%2FscRIpt%3E`.
4. Load the crafted URL in a web browser.
5. Observe that the JavaScript payload executes and triggers an alert box (`1`), confirming reflected XSS execution via filter bypass.

## POC
<img width="1339" height="536" alt="image" src="https://github.com/user-attachments/assets/5289aba1-2f8c-47a2-8218-2507e4a49d7c" />


## Impact

1. Execution of arbitrary JavaScript code in the browser of any user who visits the crafted malicious link.
2. Potential session hijacking, cookie theft, or unauthorized actions performed on behalf of the victim user.
3. Defacement or redirection of the affected web application interface.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all user-supplied data reflected in HTTP responses or rendered in the DOM.
2. Sanitize and validate all query parameters (`fname`, `lname`) before reflecting them back to the user interface rather than relying on weak blacklists or keyword filters.
3. Deploy a robust Content Security Policy (CSP) to restrict the execution of inline scripts and unauthorized script sources.

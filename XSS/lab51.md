
## Title

Reflected Cross-Site Scripting (XSS) via Unsanitized `sort` Parameter in Query String on Research Portal.

## Affected URL

https://shop.pwnedbox.site/index.php?category=physics&sort=newest

## Description

A reflected Cross-Site Scripting (XSS) vulnerability was identified in the web application's query parameter handling. The application takes user-supplied input from the query string (specifically via the `sort` parameter) and reflects it directly into the Document Object Model (DOM) or page response without proper input sanitization, encoding, or output escaping.

An attacker can craft a malicious URL containing a JavaScript payload (e.g., `<script>alert(1)</script>`) injected into the `sort` parameter. When a victim user visits the crafted URL, the malicious script executes within the context of their session in the browser, allowing the execution of arbitrary JavaScript code.

## Steps to Reproduce

1. Navigate to the target application: `https://shop.pwnedbox.site/index.php?category=physics&sort=newest`[cite: 53].
2. Identify that user-supplied query parameters (such as `sort`) are reflected directly into the page output or DOM elements.
3. Craft a malicious URL injecting a script payload into the parameter, such as: `https://shop.pwnedbox.site/index.php?category=physics&sort=newestkrishika%27%22%3E%3Cscript%3Ealert(1)%3C/script%3E`[cite: 54].
4. Load the crafted URL in a web browser.
5. Observe that the JavaScript payload executes and triggers an alert box (`1`), confirming reflected XSS execution[cite: 54].

## POC

<img width="1017" height="960" alt="image" src="https://github.com/user-attachments/assets/4a14fdc3-d5b5-4b0d-a983-c1e315b463b6" />
<img width="1393" height="499" alt="image" src="https://github.com/user-attachments/assets/3a163fe2-f2bc-40b3-8a5c-5795290b86f2" />


## Impact

1. Execution of arbitrary JavaScript code in the browser of any user who visits the crafted malicious link.
2. Potential session hijacking, cookie theft, or unauthorized actions performed on behalf of the victim user.
3. Defacement or redirection of the affected web application interface.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all user-supplied data reflected in HTTP responses or rendered in the DOM.
2. Sanitize and validate all query parameters (`sort`) before reflecting them back to the user interface.
3. Deploy a robust Content Security Policy (CSP) to restrict the execution of inline scripts and unauthorized script sources.

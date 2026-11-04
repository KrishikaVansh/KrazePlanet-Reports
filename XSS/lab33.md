## Title

Reflected Cross-Site Scripting (XSS) via Unsanitized `q` Parameter in Query String on Refseek Platform.

## Affected URL

https://refseek.pwnedbox.site/index.php

## Description

A reflected Cross-Site Scripting (XSS) vulnerability was identified in the web application's query parameter handling. The application takes user-supplied input from the query string (specifically via the `q` parameter) and reflects it directly into the Document Object Model (DOM) or page response without proper input sanitization, encoding, or output escaping.

An attacker can craft a malicious URL containing a JavaScript payload (e.g., `<scRiPt>confirm(1)</scRiPt>`) injected into the `q` parameter. When a victim user visits the crafted URL, the malicious script executes within the context of their session in the browser, allowing the execution of arbitrary JavaScript code.

## Steps to Reproduce

1. Navigate to the target application: `https://refseek.pwnedbox.site/index.php`.
2. Identify that user-supplied query parameters (such as `q` used in the search function) are reflected directly into the page output or DOM elements.
3. Craft a malicious URL injecting a script payload into the parameter, such as: `https://refseek.pwnedbox.site/index.php?q=krishika%27%22%20%3C%3E%20%3CscRiPt%3Econfirm%281%29%3C%2FscRiPt%3E`.
4. Load the crafted URL in a web browser.
5. Observe that the JavaScript payload executes and triggers a confirmation box (`1`), confirming reflected XSS execution.

## POC
<img width="1273" height="365" alt="image" src="https://github.com/user-attachments/assets/9674f7b1-fbd7-443e-a1d2-1eae28d36ab2" />


## Impact

1. Execution of arbitrary JavaScript code in the browser of any user who visits the crafted malicious link.
2. Potential session hijacking, cookie theft, or unauthorized actions performed on behalf of the victim user.
3. Defacement or redirection of the affected web application interface.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all user-supplied data reflected in HTTP responses or rendered in the DOM.
2. Sanitize and validate all query parameters (`q`) before reflecting them back to the user interface.
3. Deploy a robust Content Security Policy (CSP) to restrict the execution of inline scripts and unauthorized script sources.

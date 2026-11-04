## Title

Reflected Cross-Site Scripting (XSS) via Unsanitized `q` Parameter in Query String on Docs Platform.

## Affected URL

https://docs.pwnedbox.site/

## Description

A reflected Cross-Site Scripting (XSS) vulnerability was identified in the web application's query parameter handling. The application takes user-supplied input from the query string (specifically via the `q` parameter) and reflects it directly into the Document Object Model (DOM) or page response without proper input sanitization, encoding, or output escaping.

An attacker can craft a malicious URL containing a JavaScript payload (e.g., `<IMG src=x onerror=alert(1)>`) injected into the `q` parameter. When a victim user visits the crafted URL, the malicious script executes within the context of their session in the browser, allowing the execution of arbitrary JavaScript code.

## Steps to Reproduce

1. Navigate to the target application: `https://docs.pwnedbox.site/`.
2. Identify that user-supplied query parameters (such as `q`) are reflected directly into the page output or DOM elements.
3. Craft a malicious URL injecting a script payload into the parameter, such as: `https://docs.pwnedbox.site/?q=krishika%27%22%3E%3Ch1%3E%3C%2Fh1%3E%3CIMG%20src%3Dx%20onerror%3Dalert%281%29%3E`.
4. Load the crafted URL in a web browser.
5. Observe that the JavaScript payload executes and triggers an alert box (`1`), confirming reflected XSS execution.

## POC

<img width="1352" height="687" alt="image" src="https://github.com/user-attachments/assets/37169cd5-46c5-4ee2-ac1a-4e00acb7af56" />


## Impact

1. Execution of arbitrary JavaScript code in the browser of any user who visits the crafted malicious link.
2. Potential session hijacking, cookie theft, or unauthorized actions performed on behalf of the victim user.
3. Defacement or redirection of the affected web application interface.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all user-supplied data reflected in HTTP responses or rendered in the DOM.
2. Sanitize and validate all query parameters (`q`) before reflecting them back to the user interface.
3. Deploy a robust Content Security Policy (CSP) to restrict the execution of inline scripts and unauthorized script sources.

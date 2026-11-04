## Title

Reflected Cross-Site Scripting (XSS) via Unsanitized `title` Parameter in Query String on News Platform.

## Affected URL

https://news.pwnedbox.site/

## Description

A reflected Cross-Site Scripting (XSS) vulnerability was identified in the web application's query parameter handling. The application takes user-supplied input from the query string (specifically via the `title` parameter) and reflects it directly into the Document Object Model (DOM) or page response without proper input sanitization, encoding, or output escaping.

An attacker can craft a malicious URL containing a JavaScript payload (e.g., `krishika1'"></title><scRiPT>alert(1)</scRiPT>`) injected into the `title` parameter. When a victim user visits the crafted URL, the malicious script executes within the context of their session in the browser, allowing the execution of arbitrary JavaScript code.

## Steps to Reproduce

1. Navigate to the target application: `https://news.pwnedbox.site/`.
2. Identify that user-supplied query parameters (such as `title`) are reflected directly into the page output or DOM elements.
3. Craft a malicious URL injecting a script payload into the parameter, such as: `https://news.pwnedbox.site/?title=krishika1%27%22%3E%3C%2Ftitle%3E%3CscRiPT%3Ealert%281%29%3C%2FscRiPT%3E`.
4. Load the crafted URL in a web browser.
5. Observe that the JavaScript payload executes and triggers an alert box (`1`), confirming reflected XSS execution.

## POC
<img width="1278" height="488" alt="image" src="https://github.com/user-attachments/assets/6aefbe26-5962-44af-ba5a-2324f2383cc1" />


## Impact

1. Execution of arbitrary JavaScript code in the browser of any user who visits the crafted malicious link.
2. Potential session hijacking, cookie theft, or unauthorized actions performed on behalf of the victim user.
3. Defacement or redirection of the affected web application interface.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all user-supplied data reflected in HTTP responses or rendered in the DOM.
2. Sanitize and validate all query parameters (`title`) before reflecting them back to the user interface.
3. Deploy a robust Content Security Policy (CSP) to restrict the execution of inline scripts and unauthorized script sources.

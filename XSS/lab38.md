## Title

Reflected Cross-Site Scripting (XSS) via Unsanitized `lname` Parameter in Query String on Cookbook Platform.

## Affected URL

https://cookbook.pwnedbox.site/

## Description

A reflected Cross-Site Scripting (XSS) vulnerability was identified in the web application's query parameter handling. The application takes user-supplied input from the query string (specifically via the `lname` parameter) and reflects it directly into the Document Object Model (DOM) or page response without proper input sanitization, encoding, or output escaping.

An attacker can craft a malicious URL containing a JavaScript payload injected into the `lname` parameter. When a victim user visits the crafted URL, the malicious script executes within the context of their session in the browser, allowing the execution of arbitrary JavaScript code.

## Steps to Reproduce

1. Navigate to the target application: `https://cookbook.pwnedbox.site/`.
2. Identify that user-supplied query parameters (such as `lname`) are reflected directly into the page output or DOM elements.
3. Craft a malicious URL injecting a script payload into the parameter, such as: `https://cookbook.pwnedbox.site/?fname=krishika1%27&lname=krishika2%27%22%3C%3E%3CscRIpt%3Ealert%281%29%3C%2FscRIpt%3E`.
4. Load the crafted URL in a web browser.
5. Observe that the JavaScript payload executes and triggers an alert box (`1`), confirming reflected XSS execution via the `lname` parameter.

## POC

<img width="1236" height="335" alt="image" src="https://github.com/user-attachments/assets/345659a6-743b-421b-ae4d-a41f645be8e8" />


## Impact

1. Execution of arbitrary JavaScript code in the browser of any user who visits the crafted malicious link.
2. Potential session hijacking, cookie theft, or unauthorized actions performed on behalf of the victim user.
3. Defacement or redirection of the affected web application interface.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all user-supplied data reflected in HTTP responses or rendered in the DOM.
2. Sanitize and validate all query parameters (`lname`) before reflecting them back to the user interface.
3. Deploy a robust Content Security Policy (CSP) to restrict the execution of inline scripts and unauthorized script sources.

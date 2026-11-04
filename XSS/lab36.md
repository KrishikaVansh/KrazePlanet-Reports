## Title

Reflected Cross-Site Scripting (XSS) via Unsanitized `fname` and `lname` Parameters on KrazePlanetLabs Search Portal.

## Affected URL

https://search.pwnedbox.site/

## Description

A reflected Cross-Site Scripting (XSS) vulnerability was identified in the GET parameter reflection feature. The application takes user-supplied input from query parameters (`fname` and `lname`) and reflects them directly into the Document Object Model (DOM) or page response without proper input sanitization, encoding, or output escaping.

An attacker can craft a malicious URL containing JavaScript payloads injected into these parameters. When a victim user visits the crafted URL, the malicious scripts execute within the context of their session in the browser, allowing the execution of arbitrary JavaScript code.

## Steps to Reproduce

1. Navigate to the target application: `https://search.pwnedbox.site/`.
2. Identify that user-supplied GET parameters (`fname` and `lname`) are reflected directly into the page output or DOM elements.
3. Craft a malicious URL injecting script payloads into both parameters, such as: `https://search.pwnedbox.site/?fname=krishika1%27%22%3C%3E%3Cscript%3Ealert%281%29%3B%3C%2Fscript%3E&lname=krishika2%27%22%3C%3E%3CscRIpt%3Ealert%282%29%3B%3C%2FscRIpt%3E`.
4. Load the crafted URL in a web browser.
5. Observe that the JavaScript payloads execute and trigger alert boxes, confirming reflected XSS execution.

## POC

<img width="1349" height="765" alt="image" src="https://github.com/user-attachments/assets/a76c4610-7fc7-46cc-b71d-65408caf2bef" />

<img width="1305" height="701" alt="image" src="https://github.com/user-attachments/assets/3de78adf-b80d-486a-a1b3-8e3f58e8a48a" />


## Impact

1. Execution of arbitrary JavaScript code in the browser of any user who visits the crafted malicious link.
2. Potential session hijacking, cookie theft, or unauthorized actions performed on behalf of the victim user.
3. Defacement or redirection of the affected web application interface.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all user-supplied data reflected in HTTP responses or rendered in the DOM.
2. Sanitize and validate all query parameters (`fname`, `lname`) before reflecting them back to the user interface.
3. Deploy a robust Content Security Policy (CSP) to restrict the execution of inline scripts and unauthorized script sources.

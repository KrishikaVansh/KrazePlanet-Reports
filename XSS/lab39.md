## Title

Reflected Cross-Site Scripting (XSS) via Unsanitized `fname` and `lname` Parameters on KrazePlanet Support Portal.

## Affected URL

https://support.pwnedbox.site/

## Description

A reflected Cross-Site Scripting (XSS) vulnerability was identified in the GET parameter reflection feature. The application takes user-supplied input from query parameters (`fname` and `lname`) and reflects them directly into the Document Object Model (DOM) or page response without proper input sanitization, encoding, or output escaping.

An attacker can craft a malicious URL containing JavaScript payloads injected into these parameters. When a victim user visits the crafted URL, the malicious scripts execute within the context of their session in the browser, allowing the execution of arbitrary JavaScript code.

## Steps to Reproduce

1. Navigate to the target application: `https://support.pwnedbox.site/`.
2. Identify that user-supplied GET parameters (`fname` and `lname`) are reflected directly into the page output or DOM elements.
3. Craft a malicious URL injecting script payloads into both parameters, such as: `https://support.pwnedbox.site/?fname=krishika1%27%22%3C%3E%3CscRIpt%3Ealert%281%29%3B%3C%2FscRIpt%3E&lname=krishika2%27%22%3C%3E%3CscriPt%3E%5Calert%281%29%3C%2FscriPt%3E`.
4. Load the crafted URL in a web browser.
5. Observe that the JavaScript payload executes and triggers an alert box (`1`), confirming reflected XSS execution.

## POC

<img width="1208" height="798" alt="image" src="https://github.com/user-attachments/assets/8f0badc9-075f-4d63-83e8-a66cac0f1feb" />

<img width="665" height="314" alt="image" src="https://github.com/user-attachments/assets/0e4e18fa-1d3e-4edd-ad34-c852b2baa339" />


## Impact

1. Execution of arbitrary JavaScript code in the browser of any user who visits the crafted malicious link.
2. Potential session hijacking, cookie theft, or unauthorized actions performed on behalf of the victim user.
3. Defacement or redirection of the affected web application interface.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all user-supplied data reflected in HTTP responses or rendered in the DOM.
2. Sanitize and validate all query parameters (`fname`, `lname`) before reflecting them back to the user interface.
3. Deploy a robust Content Security Policy (CSP) to restrict the execution of inline scripts and unauthorized script sources.

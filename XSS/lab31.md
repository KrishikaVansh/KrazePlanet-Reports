## Title

Reflected Cross-Site Scripting (XSS) via Unsanitized `q` Parameter in Query String on TutorialRepublic Platform.

## Affected URL

https://tutorialrepublic.pwnedbox.site/

## Description

A reflected Cross-Site Scripting (XSS) vulnerability was identified in the web application's query parameter handling. The application takes user-supplied input from the query string (specifically via the `q` parameter) and reflects it directly into the Document Object Model (DOM) or page response without proper input sanitization, encoding, or output escaping.

An attacker can craft a malicious URL containing a JavaScript payload (e.g., `<script>alert(1);</script>`) injected into the `q` parameter. When a victim user visits the crafted URL, the malicious script executes within the context of their session in the browser, allowing the execution of arbitrary JavaScript code.

## Steps to Reproduce

1. Navigate to the target application: `https://tutorialrepublic.pwnedbox.site/`.
2. Identify that user-supplied query parameters (such as `q` used in the search bar) are reflected directly into the page output or DOM elements.
3. Craft a malicious URL injecting a script payload into the parameter, such as: `https://tutorialrepublic.pwnedbox.site/?q=krishika%1%27%3E%3Cscript%3Ealert%281%29%3B%3C%2Fscript%3E`.
4. Load the crafted URL in a web browser.
5. Observe that the JavaScript payload executes and triggers an alert box (`1`), confirming reflected XSS execution.

## POC

<img width="1468" height="851" alt="image" src="https://github.com/user-attachments/assets/ba2fda19-db3e-4050-99ff-cde1017a0bea" />
<img width="1170" height="392" alt="image" src="https://github.com/user-attachments/assets/a0b5f91d-1015-4250-894b-d3ed3f111f21" />



## Impact

1. Execution of arbitrary JavaScript code in the browser of any user who visits the crafted malicious link.
2. Potential session hijacking, cookie theft, or unauthorized actions performed on behalf of the victim user.
3. Defacement or redirection of the affected web application interface.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all user-supplied data reflected in HTTP responses or rendered in the DOM.
2. Sanitize and validate all query parameters (`q`) before reflecting them back to the user interface.
3. Deploy a robust Content Security Policy (CSP) to restrict the execution of inline scripts and unauthorized script sources.

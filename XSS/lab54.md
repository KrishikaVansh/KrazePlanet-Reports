## Title

Reflected Cross-Site Scripting (XSS) via Unsanitized `returnTo` Parameter in Redirect Endpoint on Support Platform.

## Affected URL

https://go.pwnedbox.site/

## Description

A reflected Cross-Site Scripting (XSS) vulnerability was identified in the web application's redirection and account confirmation workflow. The application accepts user-supplied input via the query parameter (`returnTo`) and reflects it directly into the Document Object Model (DOM) or page response without proper input sanitization, encoding, or output escaping.

An attacker can craft a malicious URL containing a JavaScript payload (e.g., `<script>confirm(1)</script>`) injected into the `returnTo` parameter. When a victim user visits the crafted URL, the malicious script executes within the context of their session in the browser, allowing the execution of arbitrary JavaScript code.

## Steps to Reproduce

1. Navigate to the target application: `https://go.pwnedbox.site/?returnTo=https://accounts.shopify.com`[cite: 57].
2. Identify that user-supplied query parameters (such as `returnTo`) are reflected directly into the page output or DOM elements.
3. Craft a malicious URL injecting a script payload into the parameter, such as: `https://go.pwnedbox.site/?returnTo=https://accounts.shopify.comkrishika%22%3E%3Cscript%3Econfirm(1)%3C/script%3E`[cite: 58].
4. Load the crafted URL in a web browser.
5. Observe that the JavaScript payload executes and triggers a confirmation box (`1`), confirming reflected XSS execution[cite: 58].

## POC

<img width="1358" height="1037" alt="image" src="https://github.com/user-attachments/assets/a3cdc72d-6d85-41c1-b79c-5d3525991fcd" />

<img width="1256" height="331" alt="image" src="https://github.com/user-attachments/assets/a6f2be98-df26-4209-895b-f84d4b92d6ef" />


## Impact

1. Execution of arbitrary JavaScript code in the browser of any user who visits the crafted malicious link.
2. Potential session hijacking, cookie theft, or unauthorized actions performed on behalf of the victim user.
3. Defacement or redirection of the affected web application interface.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all user-supplied data reflected in HTTP responses or rendered in the DOM.
2. Sanitize and validate all query parameters (`returnTo`) before reflecting them back to the user interface or handling redirects.
3. Deploy a robust Content Security Policy (CSP) to restrict the execution of inline scripts and unauthorized script sources.

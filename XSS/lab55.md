## Title

Reflected Cross-Site Scripting (XSS) via Unsanitized Account Parameter in URL Path on Media Platform.

## Affected URL

https://media.pwnedbox.site/index.php/account/

## Description

A reflected Cross-Site Scripting (XSS) vulnerability was identified in the web application's routing and account messaging interface. The application takes user-supplied input from the URL path (specifically within the account endpoint) and reflects it directly into the Document Object Model (DOM) or page output without proper input sanitization, encoding, or output escaping.

An attacker can craft a malicious URL containing JavaScript payloads (e.g., `<script>alert(1)</script>`) injected into the account path parameter. When a victim user visits the crafted URL, the malicious script executes within the context of their session in the browser, allowing the execution of arbitrary JavaScript code.

## Steps to Reproduce

1. Navigate to the target application: `https://media.pwnedbox.site/index.php/account/PixelWizard/messages`[cite: 55].
2. Identify that parts of the URL path (such as the username or account parameter) are reflected directly into the page response.
3. Craft a malicious URL injecting a script payload into the account path parameter, such as: `https://media.pwnedbox.site/index.php/account/krishika"><script>alert(1)</script>/messages`[cite: 56].
4. Load the crafted URL in a web browser.
5. Observe that the JavaScript payload executes and triggers an alert box (`1`), confirming reflected XSS execution[cite: 56].

## POC
<img width="1202" height="648" alt="image" src="https://github.com/user-attachments/assets/5365fb8b-9f14-458a-8450-a80b53472734" />

<img width="1234" height="216" alt="image" src="https://github.com/user-attachments/assets/35685a45-a70d-4398-bccc-a5c9fcfafdf5" />


## Impact

1. Execution of arbitrary JavaScript code in the browser of any user who visits the crafted malicious link.
2. Potential session hijacking, cookie theft, or unauthorized actions performed on behalf of the victim user.
3. Defacement or redirection of the affected web application interface.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all user-supplied data reflected in HTTP responses or rendered in the DOM.
2. Sanitize and validate all URL path parameters and input tokens before reflecting them back to the user interface.
3. Deploy a robust Content Security Policy (CSP) to restrict the execution of inline scripts and unauthorized script sources.

## Title

DOM-Based Cross-Site Scripting (XSS) via Unsanitized `location.hash` Parameter on MilesWeb Clone Platform.

## Affected URL

https://milesweb.pwnedbox.site/

## Description

A DOM-based Cross-Site Scripting (XSS) vulnerability was identified in the client-side JavaScript of the MilesWeb platform. The application insecurely reads user-controlled input from `location.hash` and writes it directly into the Document Object Model (DOM) without proper sanitization, validation, or output encoding.

When a user navigates to a crafted URL containing an HTML/JavaScript payload in the fragment identifier (hash), the client-side script parses and dynamically injects the payload into the page context. This results in arbitrary JavaScript execution within the browser session of any user opening the crafted link.

## Steps to Reproduce

1. Navigate to the target application: `https://milesweb.pwnedbox.site/`.
2. Construct a malicious URL embedding an HTML/JavaScript payload into the URL hash fragment, such as: `https://milesweb.pwnedbox.site/#krishika%22%3C%3E%3Cimg%20src=x%20onerror=alert(1)%3E`.
3. Load the crafted URL in a web browser.
4. Observe that the client-side script processes `location.hash` and executes the injected script, triggering an alert box (`1`) and confirming DOM-based XSS execution.

## POC
<img width="1547" height="570" alt="image" src="https://github.com/user-attachments/assets/0997c162-4bd1-4539-b0f0-2eb7b7f8f7e6" />


## Impact

1. Execution of arbitrary client-side JavaScript within the browser context of any user opening the crafted link.
2. Risk of session hijacking, cookie theft, or unauthorized client-side actions performed on behalf of the victim user.
3. Defacement or unauthorized manipulation of the web page interface via DOM modification.

## Remediation

1. Avoid passing raw, unsanitized input from `location.hash` directly into unsafe HTML-interpreting DOM sinks (such as `innerHTML`, `outerHTML`, or direct jQuery HTML insertion methods).
2. Use safe DOM manipulation APIs and properties (such as `textContent` or `innerText`) instead of HTML-interpreting sinks when rendering dynamic user input.
3. Implement strict input validation and context-aware output encoding if dynamic rendering of hash fragments is strictly necessary.

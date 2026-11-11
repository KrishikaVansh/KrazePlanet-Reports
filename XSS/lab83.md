## Title

DOM-Based Cross-Site Scripting (XSS) via Unsanitized `location.hash` Parameter on MyCrypto Wallet Clone Platform.

## Affected URL

https://wallet.pwnedbox.site/

## Description

A DOM-based Cross-Site Scripting (XSS) vulnerability was identified in the client-side JavaScript parameter handling of the MyCrypto platform. The application insecurely reads user-controlled input from `location.hash` (specifically processing route or state fragments like `#dashboard...`) and passes it directly to an unsafe client-side DOM sink without proper validation, sanitization, or HTML entity encoding.

When a user visits a specially crafted link containing an HTML or JavaScript payload within the URL hash fragment, the client-side script parses and dynamically injects the fragment value into the DOM. This results in arbitrary JavaScript execution within the context of the victim's browser session.

## Steps to Reproduce

1. Navigate to the target application: `https://wallet.pwnedbox.site/`.
2. Construct a malicious URL embedding an HTML/JavaScript payload into the URL hash fragment, such as: `https://wallet.pwnedbox.site/#dashboardkrishika%22%3C%3E%3Cimg%20src=x%20onerror=alert(1)%3E`.
3. Load the crafted URL in the browser.
4. Observe the client-side script processing `location.hash` and executing the injected JavaScript payload (`alert(1)`).

## POC

<img width="1476" height="857" alt="image" src="https://github.com/user-attachments/assets/4fffe5c8-bf3a-4fc2-a57d-42ab6ecb52ac" />


## Impact

1. Execution of arbitrary client-side JavaScript in the browser context of any user opening the crafted link.
2. Risk of session hijacking, session token theft via cookie/local storage extraction, or DOM manipulation.
3. Phishing or unintended actions performed on behalf of authenticated users.

## Remediation

1. Avoid writing raw input from `location.hash` directly into unsafe HTML-interpreting sinks such as `innerHTML`, `outerHTML`, or jQuery dynamic HTML insertion methods.
2. Utilize safe DOM properties such as `textContent` or `innerText` when rendering URL fragment values dynamically on the page.
3. Ensure client-side sanitization libraries (e.g., DOMPurify) or proper contextual encoding are applied if HTML rendering is strictly required.

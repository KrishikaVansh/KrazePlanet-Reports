## Title

DOM-Based Cross-Site Scripting (XSS) via Unsanitized `lever-` Parameter on HackerOne Careers Clone Platform.

## Affected URL

https://careers.pwnedbox.site/

## Description

A DOM-based Cross-Site Scripting (XSS) vulnerability was identified in the client-side JavaScript parameter handling of the HackerOne Careers platform. User input extracted from `location.search` (specifically via query parameters prefixed with `lever-`) is processed and passed directly to an unsafe client-side DOM sink without prior validation, sanitization, or HTML entity encoding.

When a user visits a specially crafted link containing an HTML or JavaScript payload within a `lever-` query parameter, the client-side script parses the parameter value and dynamically injects it into the DOM. This results in arbitrary JavaScript execution within the context of the victim's browser session.

## Steps to Reproduce

1. Navigate to the target application: `https://careers.pwnedbox.site/`.
2. Construct a malicious URL embedding an HTML/JavaScript payload into a parameter starting with `lever-`, such as: `https://careers.pwnedbox.site/?lever-krishika%22%3C%3E%3Cimg%20src=x%20onerror=alert(1)%3E`.
3. Load the crafted URL in the browser.
4. Observe the client-side script processing the query parameter and executing the injected JavaScript payload (`alert(1)`).

## POC
<img width="1024" height="471" alt="image" src="https://github.com/user-attachments/assets/3fb8d1ff-0538-4184-b727-efc8be43a6cf" />


## Impact

1. Execution of arbitrary client-side JavaScript in the browser context of any user opening the crafted link.
2. Risk of session hijacking, session token theft via cookie/local storage extraction, or DOM manipulation.
3. Phishing or unintended actions performed on behalf of authenticated users.

## Remediation

1. Avoid writing raw input from `location.search` directly into unsafe HTML-interpreting sinks such as `innerHTML`, `outerHTML`, or jQuery dynamic HTML insertion methods.
2. Utilize safe DOM properties such as `textContent` or `innerText` when rendering URL parameter values dynamically on the page.
3. Ensure client-side sanitization libraries (e.g., DOMPurify) or proper contextual encoding are applied if HTML rendering is strictly required.

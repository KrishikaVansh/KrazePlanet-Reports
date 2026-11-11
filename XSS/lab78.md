## Title

DOM-Based Cross-Site Scripting (XSS) via Unsanitized `search` Parameter on IMDb Clone Platform.

## Affected URL

https://imdb.pwnedbox.site/

## Description

A DOM-based Cross-Site Scripting (XSS) vulnerability was identified in the client-side JavaScript handling of query string parameters. The application insecurely reads user-controlled input from `location.search` (specifically via the `search` parameter) and writes it directly into the Document Object Model (DOM) without proper sanitization, validation, or output encoding.

An attacker can craft a malicious URL containing an HTML or JavaScript payload (e.g., `<img src=x onerror=alert(1)>`) within the `search` parameter. When a victim user accesses the crafted URL, the client-side script parses the parameter and unsafely inserts it into the page elements, causing the script to execute immediately within the victim's browser session.

## Steps to Reproduce

1. Navigate to the target application: `https://imdb.pwnedbox.site/`.
2. Inspect how client-side JavaScript extracts parameters from `location.search` and dynamically writes them to the DOM.
3. Craft a malicious URL injecting an HTML/JavaScript payload into the `search` parameter, such as: `https://imdb.pwnedbox.site/?search=krishika%22%3C%3E%3Cimg%20src=x%20onerror=alert(1)%3E`.
4. Load the crafted URL in a web browser.
5. Observe that the payload executes client-side, triggering an alert box (`1`) and confirming DOM-based XSS execution.

## POC

<img width="1764" height="605" alt="image" src="https://github.com/user-attachments/assets/0e95ef55-5aaf-440f-b3e7-99ab1be41fba" />
<img width="1473" height="442" alt="image" src="https://github.com/user-attachments/assets/b6001fe9-895a-444d-82d7-74313c8a9455" />


## Impact

1. Execution of arbitrary JavaScript code within the context of any user visiting the crafted malicious URL.
2. Potential session hijacking, cookie theft, or unauthorized client-side actions performed on behalf of the victim user.
3. Defacement or unauthorized client-side manipulation of the affected web application interface.

## Remediation

1. Avoid passing raw, unsanitized user input from `location.search` or `location.hash` directly into dangerous DOM sinks (such as `innerHTML`, `outerHTML`, or direct jQuery HTML insertion methods).
2. Use safe DOM manipulation APIs and properties (such as `textContent` or `innerText`) instead of HTML-interpreting sinks when rendering dynamic user input.
3. Implement robust client-side input validation and context-aware output encoding to neutralize script tags and HTML markup before DOM insertion.

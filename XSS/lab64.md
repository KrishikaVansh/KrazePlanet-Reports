## Title

Stored Cross-Site Scripting (XSS) via Unsanitized Article Input Fields on Quill Content Management System.

## Affected URL

https://kzlabs.in/subdomains/cms/

## Description

A Stored Cross-Site Scripting (XSS) vulnerability was identified in the article creation feature of the Quill content management system. The application accepts user-supplied input through article fields such as the title, body content, and tags, storing them in the database without proper server-side sanitization, HTML entity encoding, or output escaping.

When users or authors view the published articles list or dashboard, the stored malicious payload (e.g., JavaScript injected via the article creation form, such as `<img src=x onerror=alert(document.domain)>`) is rendered directly into the Document Object Model (DOM) by the application. Consequently, arbitrary JavaScript executes automatically in the browser of any user viewing the affected article sections.

## Steps to Reproduce

1. Navigate to the target application: `https://kzlabs.in/subdomains/cms/`.
2. Log into a registered user account on the Quill CMS portal.
3. Click on the **Write article** button to open the article editor.
4. Enter a malicious payload containing HTML and JavaScript tags into input fields such as the title or content body (e.g., `<img src=x onerror=alert(document.domain)>`).
5. Publish the article to store the payload in the database.
6. Refresh the page or view the **My Articles** dashboard view to observe that the script executes immediately, triggering an alert box displaying the current domain (`kzlabs.in`).

## POC

<img width="1364" height="780" alt="image" src="https://github.com/user-attachments/assets/7c1b8453-ff68-4e95-8343-a28c851135f6" />


## Impact

1. Persistent execution of arbitrary JavaScript code in the browser of any user or administrator viewing the compromised articles.
2. Session hijacking, cookie theft, and unauthorized actions performed on behalf of authenticated content creators and readers.
3. Defacement of content management dashboards and widespread compromise of platform users.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all user-generated article content before rendering it in the DOM.
2. Sanitize user input on the server side using a robust HTML sanitizer library to strip dangerous tags and event handlers (`onerror`, `script`, etc.) before storing articles in the database.
3. Deploy a strict Content Security Policy (CSP) to mitigate the impact of potential XSS injections.

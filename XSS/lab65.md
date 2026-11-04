## Title

Stored Cross-Site Scripting (XSS) via Unsanitized Profile and Question Input Fields on Acronis Forum.

## Affected URL

https://kzlabs.in/subdomains/forum/

## Description

A Stored Cross-Site Scripting (XSS) vulnerability was identified in the user profile and public question submission features of the Acronis Forum platform. The application accepts user-supplied input through fields such as the user biography, signature, question title, and question body, storing them in the database without proper server-side sanitization, HTML entity encoding, or output escaping.

When users view profile pages or forum discussion threads, the stored malicious payloads (e.g., `<img src=x onerror=alert(document.domain)>` or `<script>confirm(1)</script>`) are rendered directly into the Document Object Model (DOM) by the application. Consequently, arbitrary JavaScript executes automatically in the browser of any user viewing the affected profile or question sections.

## Steps to Reproduce

1. Navigate to the target application: `https://kzlabs.in/subdomains/forum/`.
2. Log into a registered user account on the Acronis Forum platform.
3. Navigate to **My Profile** or the **Ask a Question** section.
4. Enter a malicious payload containing HTML and JavaScript tags into input fields such as the **About Me**, **Signature**, question **Title**, or question **body** (e.g., `<img src=x onerror=alert(document.domain)>`).
5. Click **Save Profile** or **Post Your Question** to store the payload in the database.
6. Refresh the page or view the profile/question as any user to observe that the script executes immediately, triggering an alert or confirmation box displaying the domain name.

## POC

<img width="1149" height="826" alt="image" src="https://github.com/user-attachments/assets/b2184df0-116d-4186-97df-c4b0cf09c507" />


## Impact

1. Persistent execution of arbitrary JavaScript code in the browser of any user or administrator viewing the compromised profiles or questions.
2. Session hijacking, cookie theft, and unauthorized actions performed on behalf of authenticated forum users.
3. Defacement of forum pages and widespread compromise of platform participants.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all user-generated profile and forum content before rendering it in the DOM.
2. Sanitize user input on the server side using a robust HTML sanitizer library to strip dangerous tags and event handlers (`onerror`, `script`, etc.) before storing data in the database.
3. Deploy a strict Content Security Policy (CSP) to mitigate the impact of potential XSS injections.

## Title

Stored Cross-Site Scripting (XSS) via Unsanitized Viewer Reaction Input Fields on Movie Streaming Portal.

## Affected URL

https://kzlabs.in/subdomains/netflix/101

## Description

A Stored Cross-Site Scripting (XSS) vulnerability was identified in the movie viewer reactions and review submission feature. The application accepts user-supplied input through reaction input fields and stores it in the database without proper server-side sanitization, HTML entity encoding, or output escaping.

When users view the movie details page, the stored malicious payload (e.g., JavaScript injected via the viewer reactions form) is rendered directly into the Document Object Model (DOM) by the application under the **Viewer Reactions** section. Consequently, arbitrary JavaScript executes automatically in the browser of any user viewing the affected movie page.

## Steps to Reproduce

1. Navigate to the target application: `https://kzlabs.in/subdomains/netflix/101`.
2. Log into a registered user account on the streaming portal.
3. Locate a movie details page and the **How are you feeling about this movie?** review or reaction section.
4. Enter a malicious payload containing HTML and JavaScript tags into the input fields (e.g., `Krishika10'"<script>alert(2)</script>`).
5. Submit the reaction or review to store the payload in the database.
6. Refresh the page or view the movie details as any user to observe that the script executes immediately, triggering an alert box (`2`).

## POC

<img width="1024" height="832" alt="image" src="https://github.com/user-attachments/assets/2c7536f1-61d3-44e7-a3b0-81d8a816724b" />

<img width="1263" height="375" alt="image" src="https://github.com/user-attachments/assets/83a6000b-1e6f-4de7-98a1-49240b436d87" />


## Impact

1. Persistent execution of arbitrary JavaScript code in the browser of any user viewing the compromised movie details page and viewer reactions.
2. Session hijacking, cookie theft, and unauthorized actions performed on behalf of authenticated viewers.
3. Defacement of movie review sections and widespread compromise of platform users.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all user-generated reaction and review content before rendering it in the DOM.
2. Sanitize user input on the server side using a robust HTML sanitizer library to strip dangerous tags and event handlers (`onerror`, `script`, etc.) before storing reactions in the database.
3. Deploy a strict Content Security Policy (CSP) to mitigate the impact of potential XSS injections.

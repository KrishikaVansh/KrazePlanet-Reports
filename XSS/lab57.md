## Title

Stored Cross-Site Scripting (XSS) via Unsanitized Comment Input Field on Community Discussions Forum.

## Affected URL

https://community.pwnedbox.site/index.php?active_post=6

## Description

A Stored Cross-Site Scripting (XSS) vulnerability was identified in the discussion forum's comment submission feature. The application accepts user-supplied input through comment text boxes and stores it in the database without proper server-side sanitization, HTML entity encoding, or output escaping.

When other users or the author view the discussion thread, the stored payload (e.g., `<img src=x onerror=alert(3000)>`) is rendered directly into the Document Object Model (DOM) by the application. Consequently, arbitrary JavaScript executes automatically in the browser of any user viewing the affected comment section.

## Steps to Reproduce

1. Navigate to the target application: `https://community.pwnedbox.site/index.php?active_post=6`[cite: 47].
2. Log into a registered user account on the community forum[cite: 47].
3. Locate the discussion thread and the comment input box under **Join the discussion**[cite: 47].
4. Enter a malicious payload containing HTML and JavaScript tags (e.g., `krishika'"<img src=x onerror=alert(3000)>`) into the comment textarea[cite: 47].
5. Click **Comment** to submit and store the payload[cite: 47].
6. Refresh the page or view the discussion thread as another user to observe that the script executes immediately, triggering an alert box (`3000`)[cite: 48].

## POC
<img width="1492" height="1282" alt="image" src="https://github.com/user-attachments/assets/78a29636-a5b0-4608-8223-a552fe4465d1" />
<img width="1466" height="1108" alt="image" src="https://github.com/user-attachments/assets/83b9113d-e63c-4902-af2c-60314628133b" />


## Impact

1. Persistent execution of arbitrary JavaScript code in the browser of any user viewing the compromised discussion thread.
2. Session hijacking, cookie theft, and unauthorized actions performed on behalf of authenticated victims.
3. Defacement of community discussion threads and widespread compromise of forum users.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all user-generated content before rendering comments or posts in the DOM.
2. Sanitize user input on the server side using a robust HTML sanitizer library to strip dangerous tags and event handlers (`onerror`, `script`, etc.) before storing comments in the database.
3. Deploy a strict Content Security Policy (CSP) to mitigate the impact of potential XSS injections.

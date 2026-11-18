## Title

Self-Cross-Site Scripting (Self-XSS) via Unsanitized Search Input on Forms Platform.

## Affected URL

https://forms.pwnedbox.site/

## Description

A Self-Cross-Site Scripting (Self-XSS) vulnerability was identified in the search functionality of the Forms platform. User-supplied input entered directly into the search interface is reflected back into the Document Object Model (DOM) or rendered insecurely without proper sanitization, validation, or output encoding.

Because the vector requires a user to manually paste or execute the payload into their own input field, the vulnerability is classified as Self-XSS. When malicious JavaScript or HTML markup (such as `<script>alert(1)</script>` or equivalent event handlers) is entered into the search field, the script executes within the user's browser session.

## Steps to Reproduce

1. Navigate to the target application: `https://forms.pwnedbox.site/`.
2. Locate the search input field within the interface.
3. Enter an HTML or JavaScript payload into the search box, such as `<script>alert(1)</script>` or equivalent payload.
4. Trigger the search action or observe immediate DOM reflection.
5. Observe that the payload executes in the browser context, triggering an alert dialog (`1`).

## POC
<img width="1357" height="645" alt="image" src="https://github.com/user-attachments/assets/99f693c0-f221-4075-ad8a-e80ee253bfbc" />


## Impact

1. Execution of arbitrary JavaScript code within the context of the user's own browser session.
2. Potential risk if victims are socially engineered into pasting malicious scripts into their own search or input fields (leading to account manipulation, session abuse, or unauthorized actions via self-exploitation vectors).
3. Client-side interface manipulation and script execution within the user's browser environment.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all reflected user input rendered within search results or UI elements.
2. Apply input sanitization and validation on search query parameters to strip or neutralize HTML tags and script contexts.
3. Utilize safe DOM APIs (such as `textContent`) rather than unsafe sinks when rendering user-supplied search queries.

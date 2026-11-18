## Title

Self-Cross-Site Scripting (Self-XSS) via Unsanitized Name Input on Apply Platform.

## Affected URL

https://apply.pwnedbox.site/

## Description

A Self-Cross-Site Scripting (Self-XSS) vulnerability was identified in the name input interface of the Apply platform. User-supplied input entered directly into the form field ("What's your name?") is reflected back into the Document Object Model (DOM) or rendered insecurely without proper sanitization, validation, or output encoding.

Because the vector requires a user to manually paste or execute the payload into their own input field, the vulnerability is classified as Self-XSS. When malicious JavaScript or HTML markup (such as `<script>confirm(1)</script>`) is entered into the name field and submitted, the script executes within the user's browser session.

## Steps to Reproduce

1. Navigate to the target application: `https://apply.pwnedbox.site/`.
2. Locate the input field asking "What's your name?".
3. Enter an HTML or JavaScript payload into the name box, such as `krishika'" <><script>confirm(1)</script>`.
4. Click the "Enter" button to submit or trigger the reflection.
5. Observe that the payload executes in the browser context, triggering a confirmation dialog (`1`).

## POC

<img width="1311" height="521" alt="image" src="https://github.com/user-attachments/assets/fe8294cc-0091-4ffb-a1eb-d25ffa014b0a" />


## Impact

1. Execution of arbitrary JavaScript code within the context of the user's own browser session.
2. Potential risk if victims are socially engineered into pasting malicious scripts into their own input fields (leading to account manipulation, session abuse, or unauthorized actions via self-exploitation vectors).
3. Client-side interface manipulation and script execution within the user's browser environment.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all reflected user input rendered within interface elements or form handlers.
2. Apply input sanitization and validation on input fields to strip or neutralize HTML tags and script contexts.
3. Utilize safe DOM APIs (such as `textContent`) rather than unsafe sinks when rendering user-supplied input.

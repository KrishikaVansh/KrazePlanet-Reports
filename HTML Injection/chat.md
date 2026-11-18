## Title

Stored HTML Injection via Unsanitized Chat Message Input on LinkedIn Support Clone Platform.

## Affected URL

https://chat.pwnedbox.site/

## Description

A Stored HTML Injection vulnerability was identified in the live chat support widget of the LinkedIn clone platform. User-supplied input entered into the chat message field is stored and rendered directly into the Document Object Model (DOM) of the support interface without proper sanitization, validation, or output encoding.

An attacker can submit arbitrary HTML markup (such as hyperlinks, structural formatting tags, or custom elements) into the chat interface. When the message is processed and displayed, the injected HTML is rendered natively by the browser, modifying the page structure and potentially allowing phishing or UI defacement within the support dashboard context.

## Steps to Reproduce

1. Navigate to the target application: `https://chat.pwnedbox.site/`.
2. Open the live support chat widget interface.
3. Enter an HTML markup payload into the message text area, such as: `<a href="https://bing.com">Click me</a>`.
4. Click the send button to submit the message[cite: 18].
5. Observe that the injected HTML tags are rendered directly as interactive elements within the chat window.

## POC

<img width="629" height="590" alt="image" src="https://github.com/user-attachments/assets/429a3897-8e49-4011-a3d6-b3ce8bef0cba" />
<img width="526" height="561" alt="image" src="https://github.com/user-attachments/assets/b8bd019e-4099-4866-9dc2-e17ddf887d0f" />


## Impact

1. Structural modification and visual defacement of the chat support interface.
2. Potential phishing risks by spoofing legitimate links, chat components, or misleading support agents/users into clicking malicious external URLs.
3. Delivery of secondary client-side attack vectors if combined with unescaped markup rendering.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all user-supplied chat messages before rendering them in the browser.
2. Apply comprehensive input validation to strip or neutralize HTML tags and attributes from incoming chat inputs.
3. Utilize safe DOM properties (such as `textContent`) rather than unsafe HTML rendering methods when displaying chat history and messages.

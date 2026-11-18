## Title

Stored HTML Injection via Unsanitized Nickname Parameter on Romit Platform.

## Affected URL

https://transfer.pwnedbox.site/profile.php

## Description

A Stored HTML Injection vulnerability was identified in the profile settings of the Romit platform. User-supplied input entered into the "Nickname" field is stored in the application database and rendered directly into the Document Object Model (DOM) of notification emails and recipient views without proper sanitization, validation, or output encoding.

An attacker can submit arbitrary HTML markup (such as hyperlinks, structural formatting tags, or custom elements) into their nickname profile setting. When the profile details or wallet invitations are generated and sent to other users, the injected HTML is rendered natively by the email client or recipient dashboard, modifying the presentation and enabling phishing or spoofing attacks.

## Steps to Reproduce

1. Navigate to the target application profile settings: `https://transfer.pwnedbox.site/profile.php`.
2. Inject an HTML markup payload into the "Nickname" input field, such as: `<a href="https://bing.com">Click me</a>`.
3. Click "Save Changes" to store the payload[cite: 19].
4. Trigger a wallet sharing invitation or view the profile in another user's context (via `settings.php`).
5. Observe that the injected HTML tags are rendered directly as interactive components or hyperlinks within the interface and notification emails.

## POC
<img width="1081" height="795" alt="image" src="https://github.com/user-attachments/assets/ef50ae80-007f-4e91-8da5-86c92f0868cb" />

<img width="1305" height="828" alt="image" src="https://github.com/user-attachments/assets/8eba194f-3703-48ec-a7a3-e92789b45558" />

<img width="767" height="578" alt="image" src="https://github.com/user-attachments/assets/92980d8e-214e-4dad-a177-ef33232187a2" />

## Impact

1. Structural modification and visual defacement of user-facing profile views and notification templates.
2. Phishing risks by spoofing legitimate invitation links or UI components, misleading recipients into clicking malicious external URLs.
3. Delivery of secondary client-side attack vectors if combined with unescaped markup rendering.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all user-supplied profile nicknames before rendering them in web interfaces or outgoing email templates.
2. Apply comprehensive input validation to reject or strip unexpected special characters and HTML markup tags from user profile fields.
3. Utilize safe text rendering methods rather than raw HTML evaluation when displaying user metadata across shared dashboards and emails.

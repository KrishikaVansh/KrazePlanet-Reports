## Title

Stored HTML Injection via Unsanitized Profile Settings (Full Name, Username, and Bio) on GitLab Clone Platform.

## Affected URL

https://code.pwnedbox.site/index.php?view=settings

## Description

A Stored HTML Injection vulnerability was identified in the profile settings module of the GitLab clone platform. User-supplied input submitted through profile fields (such as Full Name, Username, and Bio) is stored in the database and subsequently rendered directly into the Document Object Model (DOM) across public profile pages, snippet listings, and member directories without proper sanitization, validation, or output encoding.

An attacker can submit arbitrary HTML markup (such as hyperlinks, structural formatting tags, or custom elements) into their profile attributes. When other users or administrators view the victim's profile or associated snippets, the injected HTML is interpreted and rendered natively by the browser, modifying the application page layout and enabling phishing or spoofing attacks.

## Steps to Reproduce

1. Navigate to the user profile settings page: `https://code.pwnedbox.site/index.php?view=settings`.
2. Input an HTML markup payload into the "Full Name", "Username", or "Bio" fields, such as: `<a href="https://bing.com">Click me</a>`.
3. Click the "Update Profile Settings" button to save the payload.
4. Visit the public profile or snippet page where the user metadata is displayed.
5. Observe that the injected HTML tags are rendered directly as interactive links and structural elements within the interface.

## POC

<img width="1534" height="1028" alt="image" src="https://github.com/user-attachments/assets/e028a710-7c0d-4c80-8da3-2f6a4ada16b6" />
<img width="609" height="137" alt="image" src="https://github.com/user-attachments/assets/4183b2b4-9923-4d79-aa49-7f817eab09f1" />


## Impact

1. Structural modification and visual defacement of public profile pages and code snippet views.
2. Phishing risks by spoofing legitimate links, profile components, or user interfaces to deceive visiting users.
3. Delivery of secondary client-side attack vectors if combined with unescaped markup rendering.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all user-supplied profile attributes before rendering them in public areas or snippet pages.
2. Apply comprehensive input validation to reject or strip unexpected special characters and HTML markup tags from profile input fields.
3. Utilize safe DOM properties or modern templating engines that automatically escape HTML markup by default.

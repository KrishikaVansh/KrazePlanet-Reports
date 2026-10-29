## Title

Bypassing Security Domain Restrictions via Unicode Character Substitution (`İ`) Allows Inviting Blocked Email Domains.

## Affected URL

https://frontegg.pwnedbox.site/index.php?action=invite

## Description

A business logic and input sanitization vulnerability was identified in the user invitation workflow where domain restriction checks can be bypassed using Unicode character substitution. 

The organization's policy restricts invitations to specific blacklisted domains (such as `yopmail.com`). However, the domain verification check is case-sensitive or fails to perform proper Unicode normalization when evaluating email addresses. By substituting standard ASCII characters with special or Unicode characters (such as the Turkish capital dotted `İ` or similar homoglyphs), an attacker can trick the validation filter into perceiving the domain as unique or unrestricted, while the underlying mail routing system still successfully processes and delivers the invitation to the blocked domain inbox.

## Steps to Reproduce

1. Navigate to the target application: `https://frontegg.pwnedbox.site/`.
2. Register an organization account and log into the dashboard.
3. Navigate to the user invitation section (`/index.php?action=invite`) where domain restrictions are enforced against specific providers (e.g., `yopmail.com`).
4. Attempt to invite an email address using the restricted domain normally to observe the restriction error message (`Sorry, you can't invite users with this domain due to your organization's restrictions`).
5. Craft an invitation email using a Unicode substitution variant in the domain or username (e.g., utilizing a special character like `İ` instead of standard `i`, such as `rt@yopmaİl.com` or similar homoglyph variants).
6. Submit the invitation request and observe that the application's restriction filter fails to block the modified string, successfully sending the invitation.
7. Verify successful delivery using the integrated webmail panel to confirm that the invitation email reached the blocked domain inbox.

## POC

<img width="770" height="951" alt="image" src="https://github.com/user-attachments/assets/948a3c29-cb86-4e11-a3af-2c0fa362a039" />
<img width="1105" height="609" alt="image" src="https://github.com/user-attachments/assets/1a4cfd2d-ae41-4074-b590-77dbdd73a1b2" />
<img width="1590" height="940" alt="image" src="https://github.com/user-attachments/assets/a0c37969-7d75-4d03-ae02-543cba0003b4" />

## Impact

1. Complete bypass of security domain restrictions and organization invitation policies.
2. Ability to invite unauthorized or blacklisted disposable email domains into internal organization workspaces.
3. Potential for unauthorized user onboarding, spam distribution, or shadow account creation within enterprise environments.

## Remediation

1. Implement robust Unicode normalization (such as NFKC normalization) and case-folding on all email addresses and domain strings prior to executing validation checks against domain blocklists.
2. Normalize internationalized domain names (IDN) to their Punycode representation before verifying restrictions to prevent homoglyph and special character bypasses.
3. Enforce strict server-side regex matching and allow/blocklist policies that account for alternative character sets and encodings.

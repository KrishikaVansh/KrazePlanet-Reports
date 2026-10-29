## Title

Missing Two-Factor Authentication Enforcement for Collaborators on 2FA-Required Programs Allows Access via Disabled 2FA Accounts.

## Affected URL

https://participate.pwnedbox.site/

## Description

A business logic vulnerability was identified in the bug bounty collaboration workflow where security controls enforcing two-factor authentication (2FA) are only applied during initial report submission by the primary reporter, but are inadequately validated when external users are added as collaborators.

An attacker with 2FA disabled on their account can be invited as a collaborator on reports belonging to programs that strictly mandate 2FA participation. By accepting the collaboration invite, the user successfully bypasses the program's security requirements, gaining unauthorized access to participate, view, and share bounties on 2FA-restricted programs without having 2FA enabled.

## Steps to Reproduce

1. Navigate to the target application: `https://participate.pwnedbox.site/`.
2. Log into an account with 2FA enabled (e.g., Alice Carter, `@alice`) which meets program participation requirements.
3. Submit a vulnerability report to a program that mandates 2FA (e.g., Ledgerly) and add an account with 2FA disabled as a collaborator (e.g., Mallory Reed, `@mallory` with `2FA off`).
4. Log out and sign into the collaborator account (`@mallory`) which has 2FA disabled.
5. Access the invitation or report page and accept the collaboration request.
6. Observe that the user successfully joins the report and participates in the 2FA-restricted program despite not meeting the authentication policy.

## POC

<img width="829" height="943" alt="image" src="https://github.com/user-attachments/assets/b2d81be5-56a0-49b8-9e13-146e02079f4f" />
<img width="1178" height="924" alt="image" src="https://github.com/user-attachments/assets/121103f8-40f0-4504-b78c-f5f37f069ca6" />
<img width="1050" height="903" alt="image" src="https://github.com/user-attachments/assets/3c3109fe-c187-4e55-b356-c98e2fb4e4cc" />
<img width="943" height="742" alt="image" src="https://github.com/user-attachments/assets/199ece79-9988-4ddc-8f63-d98b35703a53" />
<img width="1067" height="741" alt="image" src="https://github.com/user-attachments/assets/2b6c06f3-e564-497d-b1d0-7933c32f7738" />

## Impact

1. Complete circumvention of security policies and 2FA enforcement mandates set by platform programs.
2. Unauthorized participation of unauthenticated or loosely secured accounts in high-security bug bounty programs.
3. Weakening of overall platform access control integrity and trust boundaries.

## Remediation

1. Implement strict server-side validation during the collaborator invitation and acceptance workflow to verify that any user added to a 2FA-restricted program meets the active security requirements.
2. Automatically block collaboration requests or require 2FA enablement before allowing users to accept invites for programs that mandate two-factor authentication.
3. Ensure consistent security policy checks across both primary submission endpoints and secondary collaboration/sharing functions.


## Title

Default Credentials Lead to Administrative Access on Publicly Accessible DevDojo Voyager Admin Portal.

## Affected URL

http://107.189.16.170/admin/login

## Description

A publicly accessible DevDojo Voyager (Laravel Admin Panel) portal was found to be using default administrative credentials (`admin@admin.com` / `password`). Successful authentication grants administrative privileges within the Voyager admin dashboard, allowing full control over database records, user accounts, BREAD (Browse, Read, Edit, Add, Delete) operations, media managers, and system settings.

After authentication, the account was confirmed to have administrative privileges. Management functionality such as user impersonation, role management, database inspection, and content modification was fully accessible, indicating elevated privileges.

This exposure creates a significant security risk, as Voyager provides full CRUD access to the underlying application database. Unauthorized administrative access can be leveraged to view sensitive user records, inject malicious database entries, or take over the entire application backend.

## Steps to Reproduce

1. Navigate to the affected URL: http://107.189.16.170/admin/login
2. Open the DevDojo Voyager administration login page.
3. Authenticate using default administrative credentials (`admin@admin.com` / `password`).
4. Observe that login is successful and redirects to the admin dashboard.
5. Navigate through the management panel (Users, BREAD tools, Settings) to verify administrative privileges.


## POC

<img width="1501" height="99" alt="image" src="https://github.com/user-attachments/assets/0b444266-83af-4862-80fb-7c9ffbe167df" />

<img width="1133" height="923" alt="image" src="https://github.com/user-attachments/assets/0b08a893-3fea-40a7-be82-ef33e8505562" />

<img width="1663" height="791" alt="image" src="https://github.com/user-attachments/assets/d2e08d0c-85d2-42a5-bbf2-e9a143865a7a" />

## Impact

1. Unauthorized access to the DevDojo Voyager administration panel.
2. Exposure of sensitive application data, user accounts, emails, and database schemas.
3. Ability to perform full CRUD operations (Browse, Read, Edit, Add, Delete) on database tables via BREAD.
4. Risk of privilege escalation through user management or impersonation features.
5. Potential complete application compromise by altering database contents or injecting malicious administrative payloads.

## Remediation

1. Immediately change the default administrator email and password to strong, unique credentials.
2. Restrict public internet access to the admin panel using network controls such as VPNs, IP allowlists, or firewalls.
3. Ensure the framework and all packages are updated to the latest stable versions with security patches applied.
4. Disable default admin accounts or seeders when deploying applications to production environments.
5. Routinely audit administrative logs, user accounts, and database activities for unauthorized access or changes.


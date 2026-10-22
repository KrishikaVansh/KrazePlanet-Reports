## Title

Default Credentials Lead to Administrative Access on Publicly Accessible Bonita BPM Console.

## Affected URL

[http://<target-ip>/bonita/apps/appDirectoryBonita/home/](https://13.52.126.192/bonita/apps/appDirectoryBonita/home/?_l=en)

## Description

A publicly accessible Bonita Business Process Management (BPM) application was found to be using default administrative credentials (`install` / `install`). Successful authentication grants super administrator privileges within the Bonita Super Administrator Application dashboard, allowing full control over platform maintenance, tenant organizations, business data models (BDM), resources, applications, and license configurations.

After authentication, the account was confirmed to have elevated administrative permissions (`Super administrator`). Critical operational controls—including maintenance mode toggles, organization user management, and application deployments—were fully accessible.

This exposure creates a severe security risk, as Bonita BPM coordinates enterprise workflows, business logic, and automated operational tasks. Unauthorized super administrative access allows attackers to alter business workflows, manipulate underlying organizational data, pause running processes, or potentially access sensitive business data models.

## Steps to Reproduce

1. Navigate to the affected URL: `(https://13.52.126.192/bonita/apps/appDirectoryBonita/home/?_l=en)`
2. Access the Bonita application login page.
3. Authenticate using default credentials (`install` / `install`).
4. Observe that authentication is successful and grants administrative access.
5. Navigate through the Super Administrator dashboard (Platform maintenance, Organization, BDM, Resources) to confirm elevated privileges.

## POC

<img width="1524" height="194" alt="image" src="https://github.com/user-attachments/assets/a2098d1e-ebf7-4886-86e6-6e786175bfe7" />
<img width="1552" height="1059" alt="image" src="https://github.com/user-attachments/assets/7a76c2af-c3a6-4765-ac0c-7b69a98f15fd" />
<img width="1596" height="1127" alt="image" src="https://github.com/user-attachments/assets/268405b5-2e30-4366-b2f0-3ea643068ff9" />

## Impact

1. Unauthorized access to the Bonita Super Administrator management dashboard.
2. Capability to modify platform runtime settings, toggle maintenance modes, or pause running business processes.
3. Access to administrative organization settings, user roles, groups, and permissions.
4. Risk of unauthorized deployment or modification of Business Data Models (BDM) and platform resources.
5. Potential exposure of sensitive enterprise data managed within business workflows.

## Remediation

1. Immediately change the default credentials for the `install` account and all default administrative users within the Bonita platform.
2. Enforce strong password complexity policies for all administrator and platform user accounts.
3. Restrict access to the Bonita administration console to authorized internal users via VPN, IP allowlists, or network firewalls.
4. Disable default accounts or provisioning profiles if they are not required for active operations.
5. Regularly audit administrative logins and active user accounts for anomalous activity.


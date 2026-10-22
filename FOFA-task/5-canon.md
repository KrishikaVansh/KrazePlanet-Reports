## Title

Default Credentials Lead to Administrative Access on Publicly Accessible Canon imageRUNNER ADVANCE Portal.

## Affected URL

http://80.242.39.113:8000/

## Description

A publicly accessible Canon imageRUNNER ADVANCE (iR-ADV C3325 / C359) Remote UI portal was found to be using default administrative credentials or system PINs. Successful authentication grants System Manager administrative privileges within the management console, allowing full control over device configuration, address books, print settings, status monitoring, and system management functions.

After authentication, the account was confirmed to have administrative privileges. Management functionality such as settings/registration, direct print control, stored files access, and network configuration was fully accessible, indicating elevated privileges.

This exposure creates a significant security risk, as enterprise multi-function printers handle sensitive corporate documents, network credentials, and internal communications. Unauthorized administrative access can be leveraged to intercept scanned documents, modify device settings, hijack print jobs, or use the printer as an internal pivot point.

## Steps to Reproduce

1. Navigate to the affected URL: http://80.242.39.113:8000/
2. Open the Canon imageRUNNER ADVANCE Remote UI login page.
3. Authenticate using default System Manager ID and PIN / Administrator Login credentials.
4. Observe that login is successful and redirects to the portal dashboard.
5. Navigate through the management interface (Status Monitor, Settings/Registration, Address Book) to verify administrative privileges.


## POC
<img width="1511" height="85" alt="image" src="https://github.com/user-attachments/assets/46db79b7-62ba-42ae-88ef-4c1fac1d47a2" />
<img width="1585" height="452" alt="image" src="https://github.com/user-attachments/assets/45ef4eaf-9e78-49df-8fa0-0501468e2be6" />
<img width="1704" height="868" alt="image" src="https://github.com/user-attachments/assets/3fbc96e6-9f64-4dca-86fc-57803f5f882c" />

## Impact

1. Unauthorized access to the Canon imageRUNNER ADVANCE administrative portal.
2. Exposure of sensitive device logs, address books, network settings, and stored print/scan files.
3. Potential interception or manipulation of print jobs, scanned documents, and fax configurations.
4. Risk of device disruption or misuse by altering system parameters or locking out authorized administrators.
5. Potential exploitation of exposed device services as a foothold for internal network reconnaissance or lateral movement.

## Remediation

1. Immediately change the default System Manager ID and default PIN to strong, unique values.
2. Restrict access to the Remote UI portal using network-based controls such as internal firewalls, IP allowlists, or VPNs.
3. Disable unused remote management services or protocols if not strictly required.
4. Ensure the device firmware is updated to the latest manufacturer release with security patches applied.
5. Review and audit existing address books, user authentication settings, and stored jobs for unauthorized activities.
6. Enforce strict least-privilege access by disabling unnecessary administrator profiles.



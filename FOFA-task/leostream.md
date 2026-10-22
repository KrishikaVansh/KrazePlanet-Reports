
## Title

Default Credentials Lead to Administrative Access on Publicly Accessible Leostream Connection Portal.

## Affected URL

https://44.239.146.59/

## Description

A publicly accessible Leostream Connection Broker web management portal was found to be using default administrative credentials (`admin` / `leo`). Successful authentication grants full administrative privileges within the Leostream management dashboard, allowing complete control over virtual desktop infrastructure (VDI) pools, user assignments, licensing information, broker configurations, setup utilities, and system resource parameters.

After authentication, the account was confirmed to have administrator privileges (`Signed in as Administrator`). Management functionality—including statistics monitoring, setup wizards, configuration menus, resource allocation, and system maintenance controls—was fully accessible.

This exposure creates a critical security risk, as Leostream centrally manages remote desktop access, user sessions, and virtual machine provisioning. Unauthorized administrative access can be leveraged to intercept sessions, reassign desktops, modify connection rules, or compromise connected virtual machines and backend resources.

## Steps to Reproduce

1. Navigate to the affected URL: https://44.239.146.59/
2. Open the Leostream Connection Broker sign-in page.
3. Authenticate using default administrative credentials (`admin` / `leo`).
4. Observe that authentication is successful and redirects to the administrator dashboard.
5. Navigate through the left-hand navigation pane (Dashboards, Setup, Configuration, Resources, System) to verify administrative privileges.
6. Access broker statistics, desktop power statuses, and system controls to observe the extent of accessible functionality.

## POC

<img width="1494" height="84" alt="image" src="https://github.com/user-attachments/assets/ebfce6f3-47ac-4d6a-ad1c-0069bdc80ae6" />
<img width="1553" height="950" alt="image" src="https://github.com/user-attachments/assets/48ec1305-37c6-4c75-85f1-bdd010a6dd4b" />
<img width="1692" height="1160" alt="image" src="https://github.com/user-attachments/assets/bb269c75-b00f-417a-9936-be8a81404d55" />

## Impact

1. Unauthorized access to the Leostream Connection Broker administration console.
2. Exposure of VDI statistics, connected users, license details, and desktop power/assignment statuses.
3. Potential manipulation or disruption of virtual desktop pools, user permissions, and session routing rules.
4. Risk of unauthorized deployment or reconfiguration of remote access resources.
5. Potential exploitation of exposed session metadata and broker internals as a foothold for further attacks against enterprise infrastructure.

## Remediation

1. Immediately change the default password for the administrative `admin` account via the Leostream management interface.
2. Enforce strong, unique passwords for all administrative and user accounts defined within the broker.
3. Restrict access to the Leostream connection broker portal using network-level controls such as VPNs, IP allowlists, or firewalls.
4. Ensure the Leostream Connection Broker software is updated to the latest stable version with security patches applied.
5. Review and audit existing desktop assignments, user permissions, and system event logs for unauthorized modifications or suspicious activity.


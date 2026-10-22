
## Title

Default Credentials Lead to Administrative Access on Publicly Accessible Caimore Router Management Portal.

## Affected URL

http://111.10.236.37:81/index.asp

## Description

A publicly accessible Caimore router management portal was found to be using default administrative credentials (`admin` / `admin`). Successful authentication grants administrative privileges within the web management interface, allowing full control over basic network settings (WAN, LAN, WIFI, DDNS), advanced routing, VPN configurations, internet management (QoS, portals, firewall filters), and system parameters.

After authentication, the account was confirmed to have administrative privileges. Management functionality such as IP/MAC filtering, NAT/DMZ/UPnP configuration, routing rules, and system settings was fully accessible, indicating elevated privileges.

This exposure creates a critical security risk, as network routers manage gateway traffic, routing, and access to internal network segments. Unauthorized administrative access can be leveraged to alter routing configurations, intercept traffic, establish persistent unauthorized tunnels, or disrupt connectivity for connected users and systems.

## Steps to Reproduce

1. Navigate to the affected URL: http://111.10.236.37:81/index.asp
2. Open the Caimore router management login interface (HTTP Basic Auth or login page).
3. Authenticate using default administrative credentials (`admin` / `admin`).
4. Observe that login is successful and grants access to the dashboard.
5. Navigate through the navigation menu (Basic, Advance, VPN, Internet Management, System) to verify administrative privileges.

## POC
<img width="1475" height="215" alt="image" src="https://github.com/user-attachments/assets/b72b3c5c-aebe-42cd-8357-56f905f77cc8" />
<img width="1112" height="343" alt="image" src="https://github.com/user-attachments/assets/19461d3e-b22d-44f7-a62d-10cdd2ef7515" />
<img width="1381" height="626" alt="image" src="https://github.com/user-attachments/assets/9c92b34a-46e7-4e08-8e39-51e576a741ba" />

## Impact

1. Unauthorized access to the Caimore router management console.
2. Exposure of internal network configuration, WAN/LAN settings, Wi-Fi credentials, and routing tables.
3. Potential modification of firewall filters, NAT rules, and VPN configurations to allow unauthorized external access.
4. Risk of device disruption, denial of service, or complete network interception by altering gateway traffic parameters.
5. Potential use of the compromised router as a permanent foothold or pivot point into the internal network.

## Remediation

1. Immediately change the default administrative username and password to strong, unique credentials.
2. Restrict access to the router management interface using network-based controls such as trusted internal IP allowlists, firewalls, or disabling WAN management entirely.
3. Ensure the device firmware is updated to the latest manufacturer release with security patches applied.
4. Disable unused remote management protocols and services (such as HTTP/Telnet over WAN).
5. Routinely audit system logs, active connections, and routing tables for unauthorized changes or suspicious activity.



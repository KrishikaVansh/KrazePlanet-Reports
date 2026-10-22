
## Title

Unauthorized Access to Sensitive System Information and Configuration Settings on Publicly Accessible Crestron AirMedia / DMPS Portal.

## Affected URL

https://128.59.34.229/webView/Network

## Description

A publicly accessible Crestron presentation and control system portal was found to lack proper authentication controls, allowing unauthenticated users to access sensitive administrative endpoints such as network configuration and system status pages (`/webView/Network`, `/webView/Status`). 

Successful access exposes detailed internal system architecture, including exact device models (e.g., DMPS3-4K-STR), firmware versions, build timestamps, unique device identifiers (UUIDs), serial numbers, SDK versions, network configurations, IP addresses, subnet masks, default gateways, and hostname details.

This exposure creates a significant security risk, as enterprise presentation and automation hardware often reside on trusted internal segments or manage conference room audio/visual systems. Unauthorized access to system diagnostics and network parameters allows attackers to gather actionable intelligence for targeted attacks or manipulate configuration settings.

## Steps to Reproduce

1. Navigate to the affected URL: https://128.59.34.229/webView/Network
2. Observe that the web interface loads directly without presenting an authentication or login prompt.
3. Inspect network settings, hostnames, IP addresses, and adapter configurations.
4. Navigate to status endpoints (e.g., `/webView/Status`) to view deep system diagnostics, firmware versions, and hardware identifiers.

## POC

<img width="1513" height="134" alt="image" src="https://github.com/user-attachments/assets/c8955974-0a93-411f-ba33-24b0a92ffb2e" />
<img width="1611" height="1030" alt="image" src="https://github.com/user-attachments/assets/faaed435-db8b-40d6-8a0f-55f6c1cacad3" />
<img width="1375" height="1076" alt="image" src="https://github.com/user-attachments/assets/e388e4f4-2fea-4fbc-ba79-de4d9eb3c49e" />

## Impact

1. Unauthenticated access to internal device settings and management views.
2. Exposure of sensitive hardware information, firmware versions, build details, and UUIDs.
3. Disclosure of internal network topology, IP assignments, gateway information, and hostnames.
4. Risk of configuration tampering or disruption of presentation and room control systems.
5. Utilization of exposed system telemetry and network data as a reconnaissance foothold for lateral movement within the internal network.

## Remediation

1. Restrict public internet access to the Crestron control interface using network-level controls such as firewalls, VPNs, or internal IP allowlists.
2. Enable authentication and access control policies on the Crestron device management portal.
3. Ensure device firmware is updated to the latest manufacturer release with security patches applied.
4. Audit exposed endpoints regularly to ensure administrative interfaces are not directly reachable from untrusted networks.

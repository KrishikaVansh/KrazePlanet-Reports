## Title

Default Credentials Lead to Administrative Access on Publicly Accessible MITRE Caldera Server.

## Affected URL

http://34.71.233.200:8888/login

## Description

A publicly accessible MITRE Caldera automated adversary emulation platform was found to be using default administrative credentials (`admin` / `admin`). Successful authentication grants full administrative privileges within the Caldera web console, allowing access to campaign management, adversary profiles, abilities, agents, operations, and plugin configurations.

After authentication, the account was confirmed to have administrative privileges. Management functionality such as creating operations, deploying autonomous agents, and editing adversary profiles was fully accessible, indicating elevated administrative privileges.

This exposure creates a critical security risk, as Caldera is designed to execute automated attacks, manage C2 (Command and Control) agents, and simulate adversary behaviors. Unauthorized access can be leveraged to deploy malicious payloads, control compromised hosts, or orchestrate full-scale security operations against connected networks.

## Steps to Reproduce

1. Navigate to the affected URL: http://34.71.233.200:8888/login
2. Open the MITRE Caldera login page.
3. Authenticate using default administrative credentials (`admin` / `admin`).
4. Observe that login is successful and redirects to the dashboard.
5. Navigate through the navigation menu and configuration sections to verify administrative privileges.
6. Access campaigns, abilities, operations, and contacts to observe the extent of accessible functionality.

## POC
<img width="1530" height="155" alt="image" src="https://github.com/user-attachments/assets/821423ba-f56f-4e1e-b25e-451b43ca7524" />
<img width="1165" height="968" alt="image" src="https://github.com/user-attachments/assets/fb72a49c-6aaf-4407-b085-ab1d42097ddc" />
<img width="1588" height="986" alt="image" src="https://github.com/user-attachments/assets/e4e6110d-0a38-4b99-89bc-7b750747f123" />

## Impact

1. Unauthorized access to the MITRE Caldera command and control (C2) console.
2. Exposure of agent communication parameters, active operations, and adversary profiles.
3. Ability to deploy, control, and execute commands on connected C2 agents across compromised targets.
4. Risk of orchestrating unauthorized automated attacks, payload deployment, and lateral movement simulations.
5. Potential exposure of sensitive environment details, credentials, and internal network topology gathered by agents.

## Remediation

1. Immediately change the default administrative passwords for all Caldera user accounts.
2. Enforce strong, unique passwords for all administrative access.
3. Restrict access to the Caldera C2 server using network-based controls such as VPNs, strict IP allowlists, or firewall rules.
4. Ensure Caldera and its associated plugins are updated to the latest stable versions with security patches applied.
5. Review and audit existing agents, active operations, and profiles for unauthorized modifications or persistence mechanisms.
6. Disable or remove unnecessary plugins and enforce least-privilege access for operators.


## Title

Default Credentials Lead to Administrative Access on Publicly Accessible Apache ActiveMQ Console.

## Affected URL

http://35.198.236.139:8161/console/login

## Description

A publicly accessible Apache ActiveMQ Artemis management console was found to be using default administrative credentials (`artemis` / `artemis`). Successful authentication grants administrative privileges within the ActiveMQ Artemis console, allowing full access to Java Management Extensions (JMX) beans, system runtime information, message queues, topics, and broker configurations.

After authentication, the account was confirmed to have administrative privileges (`admin` role). Management functionality such as broker monitoring, configuration inspection, and runtime operations were fully accessible.

This exposure creates a significant security risk, as message brokers handle sensitive application messages, background job queues, and inter-service communication. Unauthorized administrative access can be leveraged to inspect queued messages, tamper with data, disrupt message processing, or gain operational insight into connected infrastructure.

## Steps to Reproduce

1. Navigate to the affected URL: http://35.198.236.139:8161/console/login
2. Open the Apache ActiveMQ Artemis console login page.
3. Authenticate using default administrative credentials (`admin` / `admin`).
4. Observe that login is successful.
5. Navigate through the console interface (JMX, Runtime) to verify administrative privileges.

## POC

Automated Detection / Scan Result
<img width="1687" height="362" alt="image" src="https://github.com/user-attachments/assets/d75ebcbb-6730-4e9c-b7dd-b630e5aaadcb" />

Login Page Exposure
<img width="1177" height="975" alt="image" src="https://github.com/user-attachments/assets/4b5da4e6-1e7d-4b9f-8bd1-f0472bfaca01" />

Authenticated Administrative Console Access
<img width="1671" height="623" alt="image" src="https://github.com/user-attachments/assets/1152f289-fa6d-44ca-b893-f1cf247cf79b" />


## Impact

1. Unauthorized access to the Apache ActiveMQ Artemis administration console.
2. Exposure of internal broker structures, JMX MBeans, runtime metrics, and system configuration parameters.
3. Potential exposure or manipulation of messages passing through queues and topics.
4. Risk of service disruption or denial of service by altering broker operations or stopping instances.
5. Potential leverage of exposed broker details for further reconnaissance and lateral movement within the network.

## Remediation

1. Immediately change default passwords for all administrative and user accounts defined in `artemis-users.properties` and associated security configuration files.
2. Enforce strong, complex passwords for all console and broker access roles.
3. Restrict access to the ActiveMQ management console using network-level controls such as firewalls, IP allowlists, or VPNs.
4. Disable the web console if remote web-based administration is not strictly required.
5. Ensure role-based access controls (RBAC) follow the principle of least privilege for console users and connected service accounts.
6. Routinely audit console logins and active administrative sessions for anomalous activity.

Title

Default Credentials Lead to Administrative Access on Publicly Accessible Apache ActiveMQ Artemis Console

Affected URL

[http://[TARGET_IP]:8161/console/login](http://35.198.236.139:8161/console/login)

Description

A publicly accessible Apache ActiveMQ Artemis administration console was found to be using default administrative credentials (artemis / artemis). Successful authentication grants administrative privileges within the ActiveMQ management dashboard, allowing access to broker metrics, JMX MBeans, runtime configurations, and message queue management functionality.

After authentication, the account was confirmed to have administrative privileges (admin / amq roles). Management functionality such as queue/topic creation, message browsing, and JMX operations was accessible, indicating elevated privileges.

This exposure creates a significant security risk, as ActiveMQ is used for enterprise message queuing and integration. Administrative access can be leveraged to intercept sensitive messages, alter messaging configurations, or potentially execute unauthorized operations via exposed JMX interfaces.

Steps to Reproduce

Navigate to the affected URL: http://[TARGET_IP]:8161/console/login

Open the ActiveMQ Artemis console login page.

Authenticate using default administrative credentials (artemis / artemis).

Observe that login is successful.

Navigate through the administration interface (e.g., JMX and Runtime tabs) to verify administrative privileges.


POC

Automated Detection / Scan Result
<img width="1687" height="362" alt="image" src="https://github.com/user-attachments/assets/d75ebcbb-6730-4e9c-b7dd-b630e5aaadcb" />

Login Page Exposure
<img width="1177" height="975" alt="image" src="https://github.com/user-attachments/assets/4b5da4e6-1e7d-4b9f-8bd1-f0472bfaca01" />

Authenticated Administrative Console Access
<img width="1671" height="623" alt="image" src="https://github.com/user-attachments/assets/1152f289-fa6d-44ca-b893-f1cf247cf79b" />


Impact

Unauthorized access to the ActiveMQ management console.

Exposure of internal broker structures, JMX MBeans, and runtime environment details.

Potential manipulation, interception, or deletion of messages in active queues and topics.

Potential execution of administrative operations via JMX interface endpoints.

Risk of service disruption or denial of service by modifying queue behaviors or broker settings.

Remediation

Immediately change the default administrative credentials in the artemis.xml or bootstrap.xml / login.config files.

Enforce strong, unique passwords for all ActiveMQ management accounts.

Restrict access to the ActiveMQ console port (default 8161) using network-based controls such as firewalls, VPNs, or IP allowlists.

Ensure ActiveMQ Artemis is updated to the latest stable release.

Disable unused management interfaces or restrict JMX exposure to local loopback interfaces where applicable.



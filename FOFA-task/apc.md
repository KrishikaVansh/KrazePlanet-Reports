## Title

Default Credentials Lead to Administrative Access on Publicly Accessible APC Network Management Card.

## Affected URL

http://210.70.26.133/Forms/login1

## Description

A publicly accessible APC Network Management Card (NMC) web interface was found to be using default administrative credentials (`apc` / `apc`). Successful authentication grants administrative access to the APC UPS Network Management Card 2 interface, allowing full control over UPS configuration, network settings, system logs, power control options, and alarm notifications.

After authentication, the account was confirmed to have administrative privileges (`apc`). Critical management interfaces—including Power Control, Device Configuration, Log Inspection, and Test Utilities—were fully accessible.

This exposure creates a critical operational risk, as uninterrupted power supply (UPS) systems manage power distribution to critical infrastructure. Unauthorized administrative access can be leveraged to remotely restart connected equipment, alter threshold parameters, modify network configurations, or shut down powered devices, potentially leading to immediate service disruption.

## Steps to Reproduce

1. Navigate to the affected URL: http://210.70.26.133/Forms/login1
2. Open the APC Network Management Card login page.
3. Authenticate using default administrative credentials (`apc` / `apc`).
4. Observe that login is successful and redirects to the management interface.
5. Navigate through the top navigation tabs (Control, Configuration, Logs) to verify administrative privileges.


## POC

![Nuclei Scanner Output showing default APC login detection](image_2f7209.png)

![APC Network Management Card Login Interface](image_2f7224.png)

![Authenticated APC Network Management Card Interface](image_2f7240.png)

## Impact

1. Unauthorized administrative access to the APC Network Management Card interface.
2. Exposure of critical device event logs, power status, battery health parameters, and network configurations.
3. Potential disruption of connected hardware by executing remote power control commands (e.g., reboot, power off).
4. Modification of system thresholds, notification rules, and SNMP or network service configurations.
5. Risk of using exposed network device settings to gain technical context for further targeted attacks against internal infrastructure.

## Remediation

1. Change the default administrative password immediately for the `apc` account via the device configuration settings.
2. Disable unnecessary default accounts or restrict administrative roles using strict passphrases.
3. Restrict public internet access to power management interfaces using network controls (e.g., VPNs, firewalls, IP allowlisting).
4. Update the APC Network Management Card firmware to the latest manufacturer release to ensure security patches are applied.
5. Review system event logs and audit configuration settings to verify no unauthorized changes were made.

<img width="1519" height="103" alt="image" src="https://github.com/user-attachments/assets/2fc1b1f2-521b-49e7-b1f3-1d40736322da" />
http://210.70.26.133/Forms/login1
<img width="1378" height="690" alt="image" src="https://github.com/user-attachments/assets/06aae976-9273-40f6-9192-b78fd6cfebc4" />
<img width="1348" height="521" alt="image" src="https://github.com/user-attachments/assets/b970cf38-2a52-4365-a55f-de0f2a5b5bd5" />

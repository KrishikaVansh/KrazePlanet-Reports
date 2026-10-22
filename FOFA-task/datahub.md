## Title

Default Credentials Lead to Administrative Access on Publicly Accessible DataHub Metadata Portal.

## Affected URL

http://20.175.65.81:9002/logIn

## Description

A publicly accessible DataHub metadata platform portal was found to be using default administrative credentials (`datahub` / `datahub`). Successful authentication grants administrative privileges within the DataHub web console, allowing access to data governance settings, glossaries, tags, applications, domains, structured properties, data sources, and organizational metadata assets.

After authentication, the account was confirmed to have administrative privileges. Management functionality such as asset governance, domain management, data products tracking, platform integrations (Databricks, PostgreSQL), and user profile settings was fully accessible, indicating elevated privileges.

This exposure creates a significant security risk, as DataHub centrally indexes enterprise data assets, data lakes, databases, and pipelines. Unauthorized administrative access can be leveraged to inspect sensitive metadata, discover data schemas, manipulate data dictionaries, or expose enterprise data topology and internal system connections.

## Steps to Reproduce

1. Navigate to the affected URL: http://20.175.65.81:9002/logIn
2. Open the DataHub login page.
3. Authenticate using default administrative credentials (`datahub` / `datahub`).
4. Observe that login is successful and redirects to the dashboard.
5. Navigate through the governance, domain, data sources, and settings sections to verify administrative privileges.
6. Access tables, data products, and platform integrations to observe the extent of accessible functionality.

## POC
<img width="1494" height="309" alt="image" src="https://github.com/user-attachments/assets/92f8bfbe-19ed-4585-b152-f03b4bf0139b" />
<img width="1649" height="1313" alt="image" src="https://github.com/user-attachments/assets/f1f2b708-ced0-406b-85d8-e59eb572d2fb" />

## Impact

1. Unauthorized access to the DataHub metadata management and governance platform.
2. Exposure of enterprise data catalogs, schemas, tables, data products, and internal data platform connections (Databricks, PostgreSQL).
3. Potential modification of metadata definitions, glossary terms, tags, and domain structures.
4. Risk of exposing sensitive business intelligence context, database names, and internal data architecture.
5. Potential use of exposed metadata insights as a reconnaissance foothold for targeted data exfiltration or attacks against connected databases.

## Remediation

1. Immediately change the default administrative credentials for the `datahub` account.
2. Enforce strong, unique passwords for all DataHub user and service accounts.
3. Restrict access to the DataHub metadata portal using network-based controls such as VPNs, IP allowlists, or firewall rules.
4. Ensure DataHub is updated to the latest stable version with security patches applied.
5. Review and audit existing metadata assets, user roles, and platform integrations for unauthorized changes.
6. Enforce least-privilege access for users interacting with enterprise metadata and data governance controls.


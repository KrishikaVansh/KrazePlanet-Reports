# Title

Default Admin Login and Potential Path Traversal Identified During Directory Fuzzing

## Affected URL

https://kzlabs.pwnedbox.site/

## Description

A web directory discovery assessment was conducted against the application to identify potentially sensitive endpoints, administrative interfaces, and file-handling functionality.

Directory enumeration was performed using `ffufscan` and `ffuf`, with the `alltech.txt` and SecLists `raft-medium-directories.txt` wordlists. The assessment identified two routes of interest: an administrative panel candidate and a files endpoint.

The administrative panel was identified at `/subdomains/adminpanel/`. 

The files endpoint was identified at `/subdomains/files/`. The application displayed an error that may warrant further investigation for a potential path traversal vulnerability. However, no successful traversal payload, unauthorized file read, or sensitive file disclosure was confirmed from the available evidence.

Accordingly, these observations represent preliminary findings rather than validated security vulnerabilities.

## Steps to Reproduce

### 1. Perform Directory Discovery

Run `ffufscan` against the target using the `alltech.txt` wordlist:

```bash
echo "https://kzlabs.pwnedbox.site/" | ffufscan --wordlist alltech.txt
```

Perform additional directory enumeration using `ffuf` and the SecLists directory wordlist:

```bash
ffuf -u https://kzlabs.pwnedbox.site/FUZZ \
-w SecLists/Discovery/Web-Content/raft-medium-directories.txt
```

Enumerate the `/subdomains/` path:

```bash
ffuf -u https://kzlabs.pwnedbox.site/subdomains/FUZZ \
-w SecLists/Discovery/Web-Content/raft-medium-directories.txt
```

### 2. Investigate the Administrative Panel

1. Navigate to https://kzlabs.pwnedbox.site/subdomains/adminpanel/
2. Observe the administrative panel response.
3. Review the authentication mechanism and determine whether default credentials are accepted.
4. Verify the privileges granted after authentication, if successful.

The supplied assessment notes mention `admin-admin` as a potential default credential pair. Successful authentication and administrative access remain unverified.

### 3. Investigate the Files Endpoint

1. Navigate to https://kzlabs.pwnedbox.site/subdomains/files/
2. Observe the error displayed by the application.
3. Investigate how the endpoint handles file paths and user-controlled input.
4. If authorized, test controlled path traversal payloads and verify whether files outside the intended directory can be accessed.

The observed error alone does not confirm path traversal.

## POC

### Finding 1: Administrative Panel Candidate

**URL:** https://kzlabs.pwnedbox.site/subdomains/adminpanel/

Directory enumeration identified an administrative panel candidate. The assessment notes reference `admin-admin` as a potential default credential pair.

**Evidence:** Screenshots `SS/image-8.png` and `SS/image-9.png`.

**Verification status:** Inconclusive. The available evidence does not demonstrate a successful login, administrative privileges, or unauthorized access to user data.

### Finding 2: Files Endpoint and Potential Path Traversal

**URL:** https://kzlabs.pwnedbox.site/subdomains/files/

The endpoint displayed an error that may indicate an area for further investigation into file path handling.

**Evidence:** Screenshot `SS/image-10.png`.

**Verification status:** Inconclusive. No confirmed path traversal, unauthorized file read, or sensitive data exposure has been demonstrated.

## Impact

No direct security impact was confirmed during the assessment.

If subsequent testing validates the suspected issues, potential impacts could include:

1. **Unauthorized administrative access:** If default credentials are accepted, an attacker may gain access to restricted administrative functionality.
2. **Sensitive data exposure:** If administrative access or file access controls are bypassed, user information or application files may be exposed.
3. **Unauthorized file access:** An exploitable path traversal vulnerability could allow access to files outside the intended directory, depending on application permissions and server configuration.
4. **Credential exposure:** Accessible configuration files or other sensitive resources could potentially disclose credentials or secrets.

These impacts are conditional and have not been established by the current evidence.

## Remediation

1. **Secure administrative access:** Ensure administrative interfaces are protected by strong, unique credentials and appropriate access controls.
2. **Eliminate default credentials:** Replace default accounts and passwords before deployment, and prevent the use of known default credential pairs.
3. **Restrict administrative endpoints:** Limit access to administrative functionality through authentication, authorization, and network restrictions where appropriate.
4. **Validate file paths:** Canonicalize and validate user-supplied paths, and ensure file operations remain within the intended storage directory.
5. **Enforce least privilege:** Ensure the application process cannot read sensitive files or directories that are unnecessary for its operation.
6. **Handle errors securely:** Avoid exposing internal filesystem paths, implementation details, or sensitive diagnostic information in application responses.
7. **Review sensitive data access:** Audit file-handling endpoints and administrative functionality for unauthorized access to user records, configuration files, and credentials.
8. **Validate findings:** Retest the suspected default credentials and path traversal behavior in an authorized environment before assigning severity or reporting either as a confirmed vulnerability.

## Conclusion

Directory enumeration identified an administrative panel candidate at `/subdomains/adminpanel/` and a files endpoint at `/subdomains/files/`. There is raised concerns about possible default credentials and path traversal behavior. However, the available evidence does not establish successful authentication, unauthorized user data access, or exploitable path traversal.

Further controlled testing is required to validate exploitability and determine whether either observation constitutes a reportable security vulnerability.

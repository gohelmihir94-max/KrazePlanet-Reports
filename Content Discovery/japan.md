## Title
Unauthorized Access to Administrative Interface via HTTP 302 Redirect

## Description
The administrative interface of `https://japan.pwnedbox.site/` was identified during directory enumeration using FFUF. The scan discovered `dashboard.php`, which returned HTTP 302 with a response size of 20,454 bytes.

The application also allowed access to the Administration Dashboard after authentication using the credentials `admin:admin`. The dashboard exposes administrative sections such as Students, Faculty, Courses, Examinations, and Admissions.

The use of default credentials presents a security risk because unauthorized users may gain administrative access if these credentials remain enabled.

**Affected URL:** https://japan.pwnedbox.site/dashboard.php

**HTTP Method:** GET

**Identified Credentials:** `admin:admin`

## Steps to Reproduce
1. Run FFUF to enumerate administrative paths on the target.
2. Identify `dashboard.php` in the results with HTTP status 302.
3. Navigate to `https://japan.pwnedbox.site/login.php`.
4. Enter the credentials `admin:admin` and sign in.
5. Observe that the Administration Dashboard becomes accessible.

## POC (Proof of Concept)

**Target:** https://japan.pwnedbox.site/

**Directory Enumeration Command:**
```bash
ffuf -u https://japan.pwnedbox.site/FUZZ \
-w ~/BugBounty/Wordlists/admin-panel-paths.txt \
-ac -mc 302
```

**Observed FFUF Result:**
```text
dashboard.php [Status: 302, Size: 20454,
Words: 10514, Lines: 1115]
```

**Login URL:**
```text
https://japan.pwnedbox.site/login.php
```

**Credentials Used:**
```text
Username: admin
Password: admin
```

**Observed Result:**

The login was successful, and the Administration Dashboard was displayed. The dashboard contained administrative sections for managing students, faculty, courses, examinations, and admissions.

**Screenshot Evidence:**
1. FFUF output identifying `dashboard.php` with HTTP 302.
<img width="1919" height="688" alt="image" src="https://github.com/user-attachments/assets/ba491b03-c1b2-4b06-ab5e-ef0fdf02229d" />


2. Successfully accessed Administration Dashboard.
<img width="1919" height="963" alt="image" src="https://github.com/user-attachments/assets/6cccafc3-0ede-4d88-8c09-2062251f6060" />

## Impact
If the default administrative credentials remain active, an attacker may gain access to the administrative interface.

Potential consequences include:

- Unauthorized access to student and faculty information.
- Exposure of application and academic records.
- Unauthorized modification of administrative data.
- Potential compromise of administrative functionality.

## Remediation
1. Immediately replace default credentials with strong, unique passwords.
2. Enforce authentication and authorization checks on every administrative endpoint.
3. Ensure unauthenticated requests cannot retrieve protected dashboard content, even when a redirect is returned.
4. Implement secure session management and invalidate sessions after logout.
5. Review administrative access logs for suspicious activity.

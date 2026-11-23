## Title
Blind Stored XSS - Informatica

## Description
The Informatica Cloud application at `https://partners.pwnedbox.site` is vulnerable to Stored Cross-Site Scripting (XSS) through the **Company Name** field in the account registration form.

The application accepts HTML containing an event handler in the Company Name field and stores the supplied value. When the stored value is rendered on the profile page, the injected JavaScript executes in the browser.

**Affected URL:** https://partners.pwnedbox.site/index.php?view=register  
**HTTP Method:** POST  
**Affected Parameter:** Company Name

## Steps to Reproduce
1. Open the registration page and fill in the required account details.
2. Enter the following payload in the Company Name field:
   ```html
   <img src=x onerror=prompt(2)>
   ```
3. Submit the registration form.
4. Observe the JavaScript prompt after the profile page loads.

## POC (Proof of Concept)
**Lab Address:** https://partners.pwnedbox.site  
**HTTP Method:** POST  
**Vulnerable Parameter:** Company Name

**Payload:**
```html
<img src=x onerror=prompt(2)>
```

**Request Parameter:**
```http
Company Name=<img src=x onerror=prompt(2)>
```

**Observed Result:**

After registration, the application stored the supplied Company Name value. When the profile page rendered the stored value, the injected JavaScript executed and displayed a browser prompt containing `2`. The profile also displayed the stored value as a broken image, confirming that the HTML was interpreted by the browser.

**Screenshot Evidence:**
1. Registration form showing the payload entered in the Company Name field.
 <img width="1912" height="1038" alt="image" src="https://github.com/user-attachments/assets/ab50fc2c-338f-4a79-974d-a0b8c01b5531" />

2. Profile page displaying the JavaScript prompt containing `2`.
<img width="1919" height="981" alt="image" src="https://github.com/user-attachments/assets/ea774b6d-d68e-4c77-880f-94847001ec34" />

3. Profile page showing the stored registration details.
<img width="1919" height="972" alt="image" src="https://github.com/user-attachments/assets/262b52e9-e48e-4edd-ad00-fd1578cb9963" />


## Impact
An attacker may store malicious JavaScript through the Company Name field. When another user views a page that renders the stored value, the script may execute in that user's browser.

Potential impact includes:
- Stored JavaScript execution in users' browsers.
- Phishing or manipulation of application content.
- Unauthorized actions within an authenticated session, depending on application protections.
- Potential exposure of information accessible to the affected user.

## Remediation
1. Apply context-aware output encoding to user-controlled data before rendering it.
2. Sanitize HTML input and prevent untrusted event-handler attributes from being rendered.
3. Validate the Company Name field on the server side.
4. Implement a restrictive Content Security Policy (CSP).

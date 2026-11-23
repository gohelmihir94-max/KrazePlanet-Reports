## Title
Stored XSS - Support Ticket

## Description
The Support Ticket functionality is vulnerable to Stored Cross-Site Scripting (XSS). User-controlled input submitted through the ticket Subject and Description fields is stored and later rendered on the Support Tickets page without proper sanitization or output encoding.

An attacker can submit a crafted image tag with an `onerror` event handler. When the stored ticket content is rendered, the browser executes the injected JavaScript.

**Affected URL:** https://digitalocean.pwnedbox.site/index.php?view=tickets  
**HTTP Method:** POST  
**Affected Parameters:** `subject`, `description`

## Steps to Reproduce
1. Log in and navigate to the Create Support Ticket page.
2. Enter the XSS payloads in the Subject and Description fields.
3. Submit the support ticket.
4. Open the Support Tickets page and observe the JavaScript prompts.

## POC (Proof of Concept)
**Lab Address:** https://digitalocean.pwnedbox.site/  
**HTTP Method:** POST  
**Vulnerable Parameters:** `subject`, `description`

**Payload:**
```html
<ImG src=x onerror=prompt(1)>
```
```html
<ImG src=x onerror=prompt(2)>
```

**Observed Result:**

Both payloads were successfully stored and rendered on the Support Tickets page. The browser executed the injected JavaScript and displayed two separate prompts containing `1` and `2`, confirming Stored XSS.

**Screenshot Evidence:**
1. Create Support Ticket form showing the injected payloads in the Subject and Description fields.
<img width="1919" height="969" alt="image" src="https://github.com/user-attachments/assets/4f3e7656-b3a6-4159-bd20-fcd36f126429" />

2. Support Tickets page displaying the second JavaScript prompt (`1`).
<img width="1914" height="983" alt="image" src="https://github.com/user-attachments/assets/115405c0-27a2-4c55-9b97-da43f43ab251" />

3. Support Tickets page displaying the first JavaScript prompt (`2`).
<img width="1909" height="979" alt="image" src="https://github.com/user-attachments/assets/1a906d98-3d4e-4720-821a-19522b3c433a" />

## Impact
An attacker may exploit this vulnerability to execute JavaScript in the browser of users viewing the affected ticket content.

- Execute arbitrary JavaScript in the victim's browser.
- Display fraudulent content or phishing interfaces.
- Perform unauthorized actions within the victim's authenticated session, depending on session protections.
- Affect multiple users who access the stored malicious ticket.

## Remediation
1. Apply context-aware output encoding before rendering user-controlled ticket content.
2. Sanitize user input using a trusted HTML sanitization library if HTML is required.
3. Avoid inserting untrusted input directly into HTML.
4. Implement a restrictive Content Security Policy (CSP) as an additional security layer.

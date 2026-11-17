## Title
Reflected XSS - Name Parameter

## Description
The application is vulnerable to Reflected Cross-Site Scripting (XSS) through the `name` parameter on the name submission page. User-controlled input is processed and reflected in the response without proper sanitization or output encoding. By submitting a crafted name containing JavaScript, an attacker can execute arbitrary JavaScript in a visitor's browser.

**Affected URL:** https://apply.pwnedbox.site/

## Steps to Reproduce
1. Open `https://apply.pwnedbox.site/`.
2. Enter the following payload in the Name field:
   ```html
   mihir''"><ScRiPt>\u0061lert('XSS')</ScRiPt>
   ```
3. Click **Enter**.
4. Observe the JavaScript alert displaying `XSS`.
5. Dismiss the alert and observe the reflected name on the page.

## POC (Proof of Concept)
**HTTP Method:** GET (as observed in the resulting page)

**Vulnerable Parameter:** `name`

**Payload:**
```html
mihir''"><ScRiPt>\u0061lert('XSS')</ScRiPt>
```

**Crafted URL:**
```text
https://apply.pwnedbox.site/?name=mihir%27%27%22%3E%3CScRiPt%3E%5Cu0061lert(%27XSS%27)%3C%2FScRiPt%3E
```

**Observed Result:**
The application executes the injected JavaScript and displays a browser alert containing `XSS`. After dismissing the alert, the page displays the reflected input as `Hello, mihir''<>`. This confirms successful JavaScript execution through the `name` parameter.

**Screenshot Evidence:**
1. Name field containing the crafted XSS payload.
<img width="1919" height="983" alt="image" src="https://github.com/user-attachments/assets/3ddea683-d32e-4ac7-abc7-30e77efcf823" />


2. Browser alert displaying `XSS`.
<img width="1919" height="986" alt="image" src="https://github.com/user-attachments/assets/f7225b83-aa92-481a-864d-3d7ab0d94d05" />


3. Page displaying the reflected name after dismissing the alert.
<img width="1917" height="1021" alt="image" src="https://github.com/user-attachments/assets/d947a9e9-7e04-45a1-b620-24b75d2802e8" />


## Impact
An attacker could create a malicious URL containing the XSS payload and trick a victim into opening it.

- JavaScript execution in the victim's browser.
- Phishing through manipulated page content.
- Potential unauthorized actions within the victim's authenticated session, depending on application protections.

## Remediation
1. Apply context-aware output encoding to all user-controlled input.
2. Validate the `name` parameter using an appropriate allowlist.
3. Avoid inserting untrusted input directly into HTML.
4. Implement a restrictive Content Security Policy (CSP).

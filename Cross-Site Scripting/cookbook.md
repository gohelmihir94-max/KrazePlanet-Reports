## Title
Reflected XSS - Multi-Parameter Script Filter Evasion

## Description

A Reflected Cross-Site Scripting (XSS) vulnerability was identified in the **Reflected XSS - Script Tag Filter Evasion (Lab 37)** challenge on KrazePlanetLabs.

The application accepts user-controlled input through the `lname` (Last Name) GET parameter. During testing, a specially crafted mixed-case script-tag payload was submitted to bypass script-tag filtering.

After submitting the payload, the browser navigated to `cookbook.pwnedbox.site`, where JavaScript execution was demonstrated through a browser prompt displaying `XSS2`.

**Affected URL:** https://cookbook.pwnedbox.site/

**HTTP Method:** GET

**Affected Parameter:** `lname`

## Steps to Reproduce

1. Open the KrazePlanetLabs Lab 37 challenge.
2. Navigate to the GET Parameter Reflection page.
3. Enter the following payload in the Last Name field:

   ```html
   "mihir'"><ScRipT>prompt("XSS2");</ScRipT>
   ```

4. Click the Submit button.
5. Observe that the browser navigates to `cookbook.pwnedbox.site`.
6. Observe the JavaScript prompt displaying `XSS2`.
7. Dismiss the prompt and observe the reflected content on the page.

## POC (Proof of Concept)

**Lab Address:** https://cookbook.pwnedbox.site/

**HTTP Method:** GET

**Vulnerable Parameter:** `lname`

**Payload:**

```html
"mihir'"><ScRipT>prompt("XSS2");</ScRipT>
```

**Observed Result:**

A JavaScript prompt dialog displaying `XSS2` appeared in the browser after submitting the payload, confirming JavaScript execution through the `lname` parameter.

**Request Parameter:**

```text
lname="mihir'"><ScRipT>prompt("XSS2");</ScRipT>
```

**Screenshot Evidence:**

<img width="1917" height="970" alt="image" src="https://github.com/user-attachments/assets/302d8973-d443-4683-8e68-3080b2686958" />
<img width="1919" height="988" alt="image" src="https://github.com/user-attachments/assets/35556f0d-916e-49e5-a4cc-9039ffbcefc1" />
<img width="1909" height="974" alt="image" src="https://github.com/user-attachments/assets/39c9f471-5335-41a9-9668-444a8fe3b391" />


## Impact

An attacker could potentially craft a malicious URL containing an XSS payload and convince a victim to open it.

Successful exploitation could result in:

- Execution of arbitrary JavaScript in the victim's browser.
- Phishing attacks through manipulated page content.
- Unauthorized modification of displayed application content.
- Potential unauthorized actions within the victim's authenticated session, depending on application security protections.

## Remediation

1. Implement context-aware output encoding for all user-controlled input before rendering it in HTML.
2. Avoid unsafe DOM manipulation methods such as `innerHTML` when handling untrusted input.
3. Implement appropriate server-side input validation and sanitization as an additional security measure.
4. Configure a restrictive Content Security Policy (CSP) to reduce the risk of unauthorized script execution.

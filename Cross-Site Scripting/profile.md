## Title
Reflected XSS - Function Name Filter

## Description

A Reflected Cross-Site Scripting (XSS) vulnerability was identified in the **Reflected XSS - Function Name Filter (Lab 40)** challenge on KrazePlanetLabs.

During testing, I examined the First Name and Last Name input fields. The application accepts user-controlled input through GET parameters. I tested a specially crafted mixed-case script-tag payload in the Last Name field to check the function-name filtering behavior.

The browser navigated to `profile.pwnedbox.site`, where the injected JavaScript successfully executed and displayed a browser prompt containing `XSS2`.

**Affected URL:** https://profile.pwnedbox.site/

**HTTP Method:** GET

**Affected Parameter:** `lname`

## Steps to Reproduce

1. Open the KrazePlanetLabs **Reflected XSS - Function Name Filter (Lab 40)** challenge.
2. Open the GET Parameter Reflection page at `https://profile.pwnedbox.site/`.
3. Locate the Last Name input field.
4. Enter the following payload:

   ```html
   "mihir'"><ScRipT>prompt("XSS2");</ScRipT>
   ```

5. Submit the form.
6. Observe the browser displaying a JavaScript prompt containing `XSS2`.
7. Dismiss the prompt and observe the resulting page.

## POC (Proof of Concept)

**Lab Address:** https://profile.pwnedbox.site/

**HTTP Method:** GET

**Vulnerable Parameter:** `lname`

**Payload:**

```html
"mihir'"><ScRipT>prompt("XSS2");</ScRipT>
```

**Observed Result:**

A JavaScript prompt dialog displaying `XSS2` appeared in the browser after submitting the payload through the `lname` parameter. This confirms successful JavaScript execution and Reflected XSS.

**Screenshot Evidence:**

1. The submitted payload in both field.
   <img width="1914" height="978" alt="image" src="https://github.com/user-attachments/assets/0dacd77d-b7ea-46ce-ade3-132f7837be69" />

2. The browser prompt displaying `XSS2`.
   <img width="1919" height="978" alt="image" src="https://github.com/user-attachments/assets/d96d11f4-ebbb-4a7e-b906-3986f18f4c09" />
  
3. The resulting page after dismissing the prompt.
   <img width="1919" height="980" alt="image" src="https://github.com/user-attachments/assets/6a73d54a-4178-476a-8bce-7c1a7d4d4009" />


## Impact

1. An attacker could potentially craft a malicious URL containing an XSS payload and convince a victim to open it.
2. Successful exploitation could allow JavaScript execution, phishing attacks, and manipulation of application content, depending on application security protections.

## Remediation

1. Implement context-aware output encoding for all user-controlled input before rendering it in HTML.
2. Avoid unsafe HTML insertion methods when handling untrusted input.
3. Apply appropriate server-side input validation and sanitization.
4. Configure a restrictive Content Security Policy (CSP) to reduce the risk of unauthorized JavaScript execution.

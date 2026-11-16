## Title
Reflected XSS - Function Name Filter

## Description

A Reflected Cross-Site Scripting (XSS) vulnerability was identified in the **Function Name Filter** challenge on KrazePlanetLabs.

The application accepts user-controlled input through the `p` GET parameter on `assets.pwnedbox.site`. During testing, a specially crafted mixed-case script-tag payload using the JavaScript `confirm()` function was submitted through this parameter.

The application reflected the supplied input in the search results heading, and the injected JavaScript successfully executed, displaying a browser confirmation dialog containing `XSS`.

**Affected URL:** https://assets.pwnedbox.site/

**HTTP Method:** GET

**Affected Parameter:** `p`

## Steps to Reproduce

1. Open the Function Name Filter challenge on KrazePlanetLabs.
2. Navigate to the Community Hub page at `https://assets.pwnedbox.site/`.
3. Locate the search filter input field.
4. Enter the following payload:

   ```html
   "mihir'"><ScRipT>confirm("XSS");</ScRipT>
   ```

5. Click the Search button.
6. Observe that the browser processes the supplied input through the `p` GET parameter.
7. A browser confirmation dialog displaying `XSS` appears, confirming successful JavaScript execution.
8. Dismiss the dialog and observe that the supplied input is reflected in the search results heading.

## POC (Proof of Concept)

**Lab Address:** https://assets.pwnedbox.site/

**HTTP Method:** GET

**Vulnerable Parameter:** `p`

**Payload:**

```html
"mihir'"><ScRipT>confirm("XSS");</ScRipT>
```

**Request URL:**

```text
https://assets.pwnedbox.site/?p=%22mihir%27%22%3C%3E%3CScRipT%3Econfirm%28%22XSS%22%29%3B%3C%2FScRipT%3E
```

**Observed Result:**

The application reflected the supplied payload in the search results heading. A JavaScript confirmation dialog displaying `XSS` appeared in the browser, confirming successful Reflected XSS through the `p` parameter.

**Screenshot Evidence:**

1. Search field containing the submitted XSS payload.
<img width="1918" height="968" alt="image" src="https://github.com/user-attachments/assets/715da4a8-233b-4a3a-a472-6aee67431b07" />

2. Browser confirmation dialog displaying `XSS`.
<img width="1919" height="968" alt="image" src="https://github.com/user-attachments/assets/ee4bf260-7665-4522-9e76-05bc11d18ac1" />

3. Search results page showing the reflected input after dismissing the dialog.
<img width="1919" height="981" alt="image" src="https://github.com/user-attachments/assets/6b25c704-8930-41b4-8ea1-9678d584ea32" />


## Impact

An attacker could potentially craft a malicious URL containing an XSS payload and trick a victim into opening it.

Successful exploitation could result in:

- Execution of arbitrary JavaScript in the victim's browser.
- Phishing attacks through manipulated page content.
- Unauthorized modification of displayed application content.
- Potential abuse of the victim's authenticated session, depending on application security protections.

## Remediation

1. Implement context-aware output encoding for all user-controlled input before rendering it in HTML.
2. Avoid unsafe HTML insertion methods when handling untrusted input.
3. Apply appropriate server-side input validation and sanitization.
4. Configure a restrictive Content Security Policy (CSP) to reduce the risk of unauthorized JavaScript execution.

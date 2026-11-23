## Title

Stored XSS - Forum Profile Signature

## Description

A Stored Cross-Site Scripting (XSS) vulnerability was identified in the profile signature functionality on `forum.pwnedbox.site`.

During testing, the application accepted user-controlled HTML in the **Signature** field under the Profile settings. The supplied payload was saved without adequate sanitization and rendered as HTML.

A malicious image element with an `onerror` event handler was successfully executed when the saved signature was rendered. Successful JavaScript execution was confirmed through a browser prompt displaying `2`.

**Affected URL:** https://forum.pwnedbox.site/

**HTTP Method:** POST (profile save operation; exact request not captured)

**Affected Parameter:** Profile Signature

## Steps to Reproduce

1. Open the lab application and navigate to **My Profile**.
2. Enter the following payload in the **Signature** field:
   ```html
   "mihir"><Img src=x onerror=prompt(2)>
   ```
3. Click **Save Profile**.
4. Open a page where the saved signature is rendered.
5. Observe the JavaScript prompt displaying `2`.

## POC (Proof of Concept)

**Lab Address:** https://forum.pwnedbox.site/

**HTTP Method:** POST (profile save operation; exact request not captured)

**Vulnerable Parameter:** Profile Signature

**Payload:**
```html
"mihir"><Img src=x onerror=prompt(2)>
```

**Request Parameter:**
```http
Signature="mihir"><Img src=x onerror=prompt(2)>
```

**Observed Result:**

The application saved the supplied payload in the user's profile signature. The saved signature was subsequently rendered as HTML, causing the injected image's `onerror` event handler to execute JavaScript.

A browser prompt displaying `2` appeared on `forum.pwnedbox.site`, confirming successful stored JavaScript execution.

**Crafted URL:**

Not applicable. This is a stored XSS vulnerability: the payload is saved in the profile and executes when the affected content is rendered. No crafted URL is required.

**Screenshot Evidence:**

1. Profile Signature field containing the XSS payload, with the live HTML preview.
<img width="1910" height="919" alt="image" src="https://github.com/user-attachments/assets/d9d1d83d-a2b2-4001-90df-c5ac5ef95329" />

2. Browser prompt displaying `2` on `forum.pwnedbox.site`.
<img width="1917" height="967" alt="image" src="https://github.com/user-attachments/assets/1205c302-e3e4-48a1-9330-7799ae1866ce" />



## Impact

An attacker who can save a malicious profile signature may cause JavaScript to execute in the browser of users who view content containing that signature.

Potential consequences include:

- Execution of unauthorized JavaScript in a victim's browser.
- Manipulation of displayed forum content.
- Phishing through injected page content.
- Potential access to information or actions available within the victim's session, depending on application security controls.

## Remediation

1. Sanitize user-supplied profile signatures using a trusted HTML sanitization library.
2. Apply context-aware output encoding when rendering user-controlled content.
3. Disallow dangerous HTML elements and event-handler attributes such as `onerror`.

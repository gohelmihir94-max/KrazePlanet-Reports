## Title

Reflected XSS - Script & Img Tag Filter

## Description

A Reflected Cross-Site Scripting (XSS) vulnerability was identified in the GET Parameter Reflection functionality on `support.pwnedbox.site`.

During testing, the application was found to accept user-controlled input through the `fname` (First Name) and `lname` (Last Name) GET parameters. By submitting specially crafted mixed-case script-tag payloads, JavaScript execution was successfully demonstrated in the browser.

The application failed to properly prevent the injected script from executing. Two separate browser alert dialogs displaying `XSS` and `XSS2` confirmed successful JavaScript execution through the respective parameters.

**Affected URL:** https://support.pwnedbox.site/

**HTTP Method:** GET

**Affected Parameters:** `fname`, `lname`

## Steps to Reproduce

1. Open the lab address: `https://support.pwnedbox.site/`
2. Navigate to the GET Parameter Reflection page.
3. Enter the following payload in the First Name field:

   ```html
   "mihir'"><ScRiPt>alert("XSS");</ScRiPt>
   ```

4. Enter the following payload in the Last Name field:

   ```html
   "mihir'"><ScRiPt>alert("XSS2");</ScRiPt>
   ```

5. Click the Submit button.
6. Observe the browser alert displaying `XSS`.
7. Dismiss the first alert and observe the second browser alert displaying `XSS2`.
8. Dismiss the second alert and observe the resulting page, where the injected input is reflected.

## POC (Proof of Concept)

**Lab Address:** https://support.pwnedbox.site/

**HTTP Method:** GET

**Vulnerable Parameters:**
- `fname`
- `lname`

**Payload 1 – First Name (`fname`):**

```html
"mihir'"><ScRiPt>alert("XSS");</ScRiPt>
```

**Payload 2 – Last Name (`lname`):**

```html
"mihir'"><ScRiPt>alert("XSS2");</ScRiPt>
```

**Observed Result:**

The application processed the supplied payloads through both GET parameters. JavaScript execution was successfully confirmed through two browser alert dialogs:

- First alert: `XSS`
- Second alert: `XSS2`

This demonstrates that the injected script executed through both the `fname` and `lname` parameters.

**Screenshot Evidence:**

1. Screenshot showing the submitted payloads in the First Name and Last Name fields.
<img width="1919" height="969" alt="image" src="https://github.com/user-attachments/assets/ac8d4a87-035b-47be-8425-322c2a0ac292" />

2. Screenshot showing the first JavaScript alert displaying `XSS`.
<img width="1919" height="985" alt="image" src="https://github.com/user-attachments/assets/0ad2dcb8-ec57-428b-af1d-c3ff65d1e290" />

3. Screenshot showing the second JavaScript alert displaying `XSS2`.
<img width="1919" height="986" alt="image" src="https://github.com/user-attachments/assets/e39bdc45-4d67-4a7c-b934-119f97e47a8a" />

4. Screenshot showing the resulting page after dismissing the alerts.
<img width="1919" height="969" alt="image" src="https://github.com/user-attachments/assets/af218a08-200a-4422-a5eb-09c8c4c4cd7b" />


## Impact

An attacker could potentially craft a malicious URL containing an XSS payload and trick a victim into opening it.

Successful exploitation could result in:

- Execution of arbitrary JavaScript in the victim's browser.
- Phishing attacks through manipulated page content.
- Unauthorized modification of displayed application content.
- Potential abuse of the victim's authenticated session, depending on application security protections.

## Remediation

1. Implement context-aware output encoding for all user-controlled input before rendering it in HTML.
2. Properly validate and sanitize user-supplied values in the `fname` and `lname` parameters.
3. Avoid unsafe HTML insertion methods such as `innerHTML` when processing untrusted input.
4. Implement a restrictive Content Security Policy (CSP) to reduce the risk of unauthorized script execution.
5. Ensure that HTML tag filtering does not rely solely on case-sensitive string matching.

## Title

Reflected XSS - Case-Insensitive Filter Bypass

## Description

A Reflected Cross-Site Scripting (XSS) vulnerability was identified in the GET Parameter Reflection functionality on `directory.pwnedbox.site`.

During testing, the application was found to accept user-controlled input through the `fname` (First Name) and `lname` (Last Name) GET parameters.

A specially crafted mixed-case script-tag payload using `<ScRiPt>` was submitted through both parameters. The application failed to properly filter or encode the injected HTML, allowing JavaScript execution in the browser.

Successful execution was confirmed through two browser alert dialogs displaying `XSS` and `XSS2`.

**Affected URL:** https://directory.pwnedbox.site/

**HTTP Method:** GET

**Affected Parameters:** `fname`, `lname`

## Steps to Reproduce

1. Open the lab address: `https://directory.pwnedbox.site/`
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
6. Observe the first JavaScript alert displaying `XSS`.
7. Dismiss the first alert.
8. Observe the second JavaScript alert displaying `XSS2`.
9. Dismiss the second alert and observe the resulting page.

## POC (Proof of Concept)

**Lab Address:** https://directory.pwnedbox.site/

**HTTP Method:** GET

**Vulnerable Parameters:**
- `fname` – First Name
- `lname` – Last Name

**Payload 1 – fname:**

```html
"mihir'"><ScRiPt>alert("XSS");</ScRiPt>
```

**Payload 2 – lname:**

```html
"mihir'"><ScRiPt>alert("XSS2");</ScRiPt>
```

**Observed Result:**

The application processed both supplied payloads and executed the injected JavaScript.

The following results were observed:

- `fname`: JavaScript alert displaying `XSS`.
- `lname`: JavaScript alert displaying `XSS2`.

The successful execution of both payloads confirms Reflected XSS through both GET parameters.

**Screenshot Evidence:**

1. Screenshot showing the submitted payloads in both First Name and Last Name fields.
<img width="1919" height="980" alt="image" src="https://github.com/user-attachments/assets/5e39a7f9-f52c-4525-a849-bcc78ef6b83a" />

2. Screenshot showing the first browser alert displaying `XSS`.
<img width="1919" height="983" alt="image" src="https://github.com/user-attachments/assets/e3771520-a96a-4c50-ae53-0f2acefe1d9a" />

3. Screenshot showing the second browser alert displaying `XSS2`.
<img width="1919" height="977" alt="image" src="https://github.com/user-attachments/assets/d8ce5561-c7e4-4772-91d6-3fb3420c108c" />

4. Screenshot showing the resulting page after dismissing the alerts.
<img width="1919" height="979" alt="image" src="https://github.com/user-attachments/assets/7c2efd65-f247-45f4-8ae6-6e8dc76ad448" />


## Impact

An attacker could potentially craft a malicious URL containing an XSS payload and trick a victim into opening it.

Successful exploitation could result in:

- Execution of arbitrary JavaScript in the victim's browser.
- Phishing attacks through manipulated page content.
- Unauthorized modification of displayed application content.
- Potential abuse of the victim's authenticated session, depending on application security protections.

## Remediation

1. Implement context-aware output encoding for all user-controlled input before rendering it in HTML.
2. Ensure HTML tag filtering is case-insensitive and does not rely solely on simple string matching.
3. Validate and safely handle user-supplied values in the `fname` and `lname` parameters.
4. Avoid unsafe HTML insertion methods such as `innerHTML` when processing untrusted input.
5. Implement a restrictive Content Security Policy (CSP) as an additional security layer.

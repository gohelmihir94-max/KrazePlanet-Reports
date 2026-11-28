## Title
CSP Bypass - Unsafe Inline Scripts

## Description
**Affected URL:** https://space.pwnedbox.site/

The application is vulnerable to Reflected Cross-Site Scripting (XSS) through the `fname` and `lname` GET parameters. The backend directly outputs user-controlled input without proper HTML encoding, allowing injected JavaScript to execute in the browser.

The application's Content Security Policy (CSP) includes `script-src 'self' 'unsafe-inline'`, which permits inline JavaScript execution. As a result, the policy does not prevent the demonstrated XSS payloads from executing.

**HTTP Method:** GET

**Affected Parameters:** `fname`, `lname`

## Steps to Reproduce
1. Open `https://space.pwnedbox.site/`.
2. Enter the following payloads in the First Name and Last Name fields:
   - First Name: `<script>alert('XSS')</script>`
   - Last Name: `<script>alert('XSS1')</script>`
3. Submit the form.
4. Observe both JavaScript alert boxes executing successfully.

## POC (Proof of Concept)

**Lab Address:** https://space.pwnedbox.site/

**HTTP Method:** GET

**Vulnerable Parameters:** `fname`, `lname`

**Payloads:**

First Name:
```html
<script>alert('XSS')</script>
```

Last Name:
```html
<script>alert('XSS1')</script>
```

**Request URL:**
```text
https://space.pwnedbox.site/?fname=%3Cscript%3Ealert%28%27XSS%27%29%3C%2Fscript%3E&lname=%3Cscript%3Ealert%28%27XSS1%27%29%3C%2Fscript%3E
```

**Observed Result:**

Both injected JavaScript payloads executed successfully, displaying separate browser alert boxes containing `XSS` and `XSS1`.

The backend source code also shows direct output of both GET parameters:
```php
if(isset($_GET["fname"]) && isset($_GET["lname"])){
    echo $_GET["fname"];
    echo $_GET["lname"];
}
```

The response CSP contains:
```text
script-src 'self' 'unsafe-inline' https://cdn.jsdelivr.net;
```

**Screenshot Evidence:**
1. CSP policy, backend source code, and both injected payloads.
<img width="1919" height="970" alt="image" src="https://github.com/user-attachments/assets/ec46f088-fd1f-4d6c-a4e5-9f805e85e046" />

2. First JavaScript alert displaying `XSS`.
<img width="1919" height="1021" alt="image" src="https://github.com/user-attachments/assets/a3d5c497-d934-45d9-a55b-9cd47b68bdfc" />

3. Second JavaScript alert displaying `XSS1`.
<img width="1919" height="970" alt="image" src="https://github.com/user-attachments/assets/2b28e949-d22d-4183-8ae8-a30c91e17e11" />


## Impact
An attacker could craft a malicious URL and trick a victim into opening it, resulting in JavaScript execution within the application's origin.

Potential consequences include:

- It allows attackers to hijack user sessions if session protections permit.
- It potentially leads to full account takeover depending on available application functionality.
- It allows unauthorized actions within the vulnerable application's security context.
- It may expose sensitive information accessible to the victim's browser session.

## Remediation
1. Apply context-aware output encoding to all user-controlled input before rendering it in HTML.
2. Never directly output GET parameters using `echo` without proper encoding.
3. Validate and safely handle the `fname` and `lname` parameters.
4. Prevent user-controlled input from being interpreted as executable HTML or JavaScript.

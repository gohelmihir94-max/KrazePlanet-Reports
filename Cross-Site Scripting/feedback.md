# Reflected XSS - Script Tag Filter Evasion


## Description

I tested the First Name and Last Name fields in the GET Parameter Reflection lab. During testing, I entered specially crafted mixed-case script-tag payloads into both fields to check whether the application's script-tag filter could be bypassed.

After clicking Submit, the browser navigated from `search.pwnedbox.site` to `feedback.pwnedbox.site`. I observed that both payloads executed successfully. The first parameter displayed a JavaScript prompt containing `XSS1`, while the second displayed `XSS2`.

This testing confirmed that both `fname` and `lname` parameters are vulnerable to Reflected Cross-Site Scripting.

## Steps to Reproduce

1. Open `https://search.pwnedbox.site/`.
2. Navigate to the GET Parameter Reflection lab.
3. Enter the following payload in the First Name field:

   ```html
   "mihir'"><ScRipT>prompt("XSS1");</ScRipT>
   ```

4. Enter the following payload in the Last Name field:

   ```html
   "mihir'"><ScRipT>prompt("XSS2");</ScRipT>
   ```

5. Click the Submit button.
6. Observe the browser navigating to the vulnerable lab address: `https://feedback.pwnedbox.site/`.
7. Observe the JavaScript prompt displaying `XSS1`.
8. Dismiss the first prompt and observe the second prompt displaying `XSS2`.

## POC (Proof of Concept)

**Target URL:** `https://feedback.pwnedbox.site/`

**HTTP Method:** GET

**Vulnerable Parameters:** `fname`, `lname`

**1. First Name (`fname`)**

Payload:
```html
"mihir'"><ScRipT>prompt("XSS1");</ScRipT>
```

Result: A JavaScript prompt appeared displaying `XSS1`, confirming successful execution through the `fname` parameter.

**2. Last Name (`lname`)**

Payload:
```html
"mihir'"><ScRipT>prompt("XSS2");</ScRipT>
```

Result: A JavaScript prompt appeared displaying `XSS2`, confirming successful execution through the `lname` parameter.

**Request URL:**

```text
https://feedback.pwnedbox.site/?fname=%22mihir%27%22%3C%3E%3CScRipT%3Eprompt%28%22XSS1%22%29%3B%3C%2FScRipT%3E&lname=%22mihir%27%22%3C%3E%3CScRipT%3Eprompt%28%22XSS2%22%29%3B%3C%2FScRipT%3E
```

**Screenshots:**

(Keep your four existing screenshots here.)
<img width="1915" height="928" alt="image" src="https://github.com/user-attachments/assets/4c6fc968-52f2-4c0b-a5a7-5a3f3aefaa88" />
<img width="1914" height="1021" alt="image" src="https://github.com/user-attachments/assets/76a9b18b-dc69-4e2a-a18f-515434831ac7" />
<img width="1919" height="976" alt="image" src="https://github.com/user-attachments/assets/a9730bc5-f20c-47b6-956f-ff50159a7aa3" />
<img width="1919" height="985" alt="image" src="https://github.com/user-attachments/assets/e1ffe87b-c518-4ee0-8235-47419481aaaf" />


Both parameters successfully demonstrated JavaScript execution using the mixed-case `<ScRipT>` tag.

## Impact

An attacker could potentially exploit this vulnerability by crafting a malicious URL containing an XSS payload and convincing a victim to open it.

Successful exploitation could lead to:

- Execution of arbitrary JavaScript in the victim's browser.
- Phishing attacks through manipulated page content.
- Unauthorized modification of application content.
- Performing actions within the victim's authenticated session, subject to applicable security protections.

## Remediation

1. Avoid unsafe DOM manipulation methods such as `innerHTML` when handling untrusted input.
2. Implement a restrictive Content Security Policy (CSP) as an additional security layer.
3. Apply appropriate server-side input validation and sanitization as defense in depth.

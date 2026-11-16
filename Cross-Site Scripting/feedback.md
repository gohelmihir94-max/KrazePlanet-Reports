# Reflected XSS - Script Tag Filter Evasion

## Description

A Reflected Cross-Site Scripting (XSS) vulnerability was identified in the **Reflected XSS - Script Tag Filter Evasion (Lab 37)** challenge on KrazePlanetLabs.

The application accepts user-controlled input through two GET parameters, `fname` (First Name) and `lname` (Last Name). By submitting specially crafted mixed-case script-tag payloads, JavaScript execution was successfully demonstrated in both parameters.

The application navigates from `search.pwnedbox.site` to `feedback.pwnedbox.site`, where the supplied payloads trigger JavaScript prompt dialogs.

**Affected URL:**
`https://feedback.pwnedbox.site/`

**HTTP Method:** GET

**Affected Parameters:**
- `fname`
- `lname`

## Steps to Reproduce

1. Open the lab URL: `https://search.pwnedbox.site/`
2. Navigate to the **GET Parameter Reflection** lab.
3. Enter the following payload in the First Name field:

   ```html
   "mihir'"><ScRipT>prompt("XSS1");</ScRipT>
   ```

4. Enter the following payload in the Last Name field:

   ```html
   "mihir'"><ScRipT>prompt("XSS2");</ScRipT>
   ```

5. Click the Submit button.
6. Observe that the browser navigates to `feedback.pwnedbox.site`.
7. A JavaScript prompt dialog displaying `XSS1` appears.
8. After dismissing the first dialog, another JavaScript prompt displaying `XSS2` appears.

## POC (Proof of Concept)

**First Name Parameter (`fname`):**

```html
"mihir'"><ScRipT>prompt("XSS1");</ScRipT>
```

**Observed Result:** JavaScript execution was confirmed through a browser prompt displaying `XSS1`.

**Last Name Parameter (`lname`):**

```html
"mihir'"><ScRipT>prompt("XSS2");</ScRipT>
```

**Observed Result:** JavaScript execution was confirmed through a browser prompt displaying `XSS2`.

**Request URL:**

```text
https://feedback.pwnedbox.site/?fname=%22mihir%27%22%3C%3E%3CScRipT%3Eprompt%28%22XSS1%22%29%3B%3C%2FScRipT%3E&lname=%22mihir%27%22%3C%3E%3CScRipT%3Eprompt%28%22XSS2%22%29%3B%3C%2FScRipT%3E
```
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

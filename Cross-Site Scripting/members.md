## Title

Stored XSS - Product Review Functionality

## Description

A Stored Cross-Site Scripting (XSS) vulnerability was identified in the product review functionality on `members.pwnedbox.site`.

During testing, user-controlled input was submitted through the product review fields, including the Headline / Short Title and Detailed Review Comments fields.

The application successfully stored and rendered the supplied HTML payload. A JavaScript prompt displaying `2` was triggered when the product page was loaded, confirming JavaScript execution.

**Affected URL:** https://members.pwnedbox.site/index.php?product_id=3

**HTTP Method:** POST (review submission; confirm using the captured request)

**Affected Parameters:** Product review headline and detailed review comments (exact parameter names not visible in the screenshots)

## Steps to Reproduce

1. Open the product page: `https://members.pwnedbox.site/index.php?product_id=3`.
2. Navigate to the Write a Customer Review section.
3. Enter the following payload in the Detailed Review Comments field:

   ```html
   "><img src=x onerror=prompt(2)>
   ```

4. Submit the product review.
5. Reload or revisit the product page.
6. Observe the JavaScript prompt displaying `2`.

## POC (Proof of Concept)

**Lab Address:** https://members.pwnedbox.site/index.php?product_id=3

**HTTP Method:** POST

**Vulnerable Input:** Detailed Review Comments

**Payload:**
```html
"><img src=x onerror=prompt(2)>
```

**Request Parameter:**

The exact POST parameter names and request body were not captured in the supplied screenshots. Capture the submission in Burp Suite to document the complete HTTP request.

**Observed Result:**

The application displayed the submitted review successfully. When the product page was loaded, a JavaScript prompt displaying `2` appeared, confirming that the submitted payload was executed.

**Screenshot Evidence:**
1. Product review form containing the injected payload.
<img width="1919" height="978" alt="image" src="https://github.com/user-attachments/assets/3febaaf7-e478-42d7-9eed-d8f77525609e" />


2. JavaScript prompt displaying `2`.
 <img width="1919" height="978" alt="image" src="https://github.com/user-attachments/assets/ccc2324c-c30d-4035-bedc-6417e2a66763" />

## Impact

- It allows attackers to hijack user session
- It potentially leads to full account takeover
- It allows to perform unauthorized actions within the vulnerable application

## Remediation

- Filter out HTML tags like: `<script>`, `<img>`, `<svg>` from the Report Name field before saving anything to the database
- Filter out JavaScript methods like: `alert()`, `confirm()`, `prompt()` so even if a tag slips through the method won't execute
- If you're using PHP then use `htmlspecialchars()` function before rendering any user input back to the page
- Use Cloudflare as they have so many WAF rules that almost all XSS payloads will be blocked automatically before even reaching the server

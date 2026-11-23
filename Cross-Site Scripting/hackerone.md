## Title
Stored Cross-Site Scripting (XSS) in Vulnerability Report Fields

## Description
The vulnerability report submission form on `hackerone.pwnedbox.site` accepts HTML input in the Title, Description, and Impact fields without properly sanitizing it. An attacker can inject JavaScript using an HTML event handler.

After submitting the report, the injected JavaScript executes when the submitted report is viewed on the dashboard.

**Affected URL:** https://hackerone.pwnedbox.site/index.php?view=submit

**HTTP Method:** POST

**Affected Parameters:** `title`, `description`, `impact`

## Steps to Reproduce
1. Open the vulnerability report submission page.
2. Enter the XSS payloads into the Title, Description, and Impact fields.
3. Submit the report.
4. Open the submitted report from the dashboard.
5. Observe the JavaScript prompt dialogs.

## POC (Proof of Concept)
**Lab Address:** https://hackerone.pwnedbox.site/

**HTTP Method:** POST

**Vulnerable Parameters:** `title`, `description`, `impact`

**Payloads:**
```html
<img src=x onerror=prompt(1)>
```
```html
<img src=x onerror=prompt(2)>
```
```html
<img src=x onerror=prompt(3)>
```

**Request Parameters:**
```http
title=<img src=x onerror=prompt(1)>
description=<img src=x onerror=prompt(2)>
impact=<img src=x onerror=prompt(3)>
```

**Observed Result:**

After submitting the report and opening it from the dashboard, JavaScript executed from all three fields. The browser displayed separate prompt dialogs containing `1`, `2`, and `3`, confirming stored XSS in the Title, Description, and Impact fields.

**Screenshot Evidence:**
1. Report submission form showing the injected payloads in the Title and Description fields.
<img width="1919" height="972" alt="image" src="https://github.com/user-attachments/assets/4ed3aa93-a663-4e30-baf9-2d044a397330" />

2. Impact field containing the third XSS payload.
<img width="1915" height="971" alt="image" src="https://github.com/user-attachments/assets/a1d3f0f9-6cf4-4acf-93ac-39885938eae7" />

3. Submitted report displaying the prompt dialog with value `2`.
<img width="1919" height="1034" alt="image" src="https://github.com/user-attachments/assets/1aa90a91-f394-4d7c-9c5c-506b371eb848" />

4. Prompt dialog displaying value `3`.
<img width="1919" height="978" alt="image" src="https://github.com/user-attachments/assets/7d1e2f25-a5bf-4758-be21-f2164bd919dd" />

5. Prompt dialog displaying value `1`.
<img width="1919" height="984" alt="image" src="https://github.com/user-attachments/assets/e849a98b-4b9c-4b86-aaee-94b5b2a299b1" />


## Impact
An attacker could inject JavaScript that executes in the browser of users viewing the affected report.

- Execute arbitrary JavaScript in the context of the application.
- Display misleading content or phishing interfaces.
- Modify the report page's visible content.
- Potentially perform actions using the victim's authenticated session, depending on application security controls.

## Remediation
1. Apply context-aware output encoding to all user-controlled report fields before rendering them.
2. Sanitize HTML input using a well-maintained HTML sanitization library if HTML is not required.
3. Implement a restrictive Content Security Policy (CSP) as an additional security layer.
4. Avoid rendering user-submitted content as raw HTML.

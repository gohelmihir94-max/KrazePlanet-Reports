## Title
Reflected XSS - Encoding Bypass Attempts

## Description
The application at `mail.pwnedbox.site` reflects user-controlled GET parameter values in the response. During testing, multiple parameters were identified as reflected using x8. Further manual testing confirmed that JavaScript could execute through four parameters: `page.id`, `page`, `id`, and `cat`.

The payload used mixed-case script tags and a Unicode escape sequence (`\u0061`) to bypass input filtering and trigger JavaScript execution. Browser alert dialogs confirmed successful reflected XSS.

**Affected URL:** https://mail.pwnedbox.site/  
**HTTP Method:** GET  
**Affected Parameters:** `page.id`, `page`, `id`, `cat`

## Steps to Reproduce
1. Open the affected URL and identify reflected GET parameters using x8.
2. Test the identified parameters individually with the supplied XSS payload.
3. Open the crafted URL in a browser.
4. Observe the JavaScript alert dialog.

## POC (Proof of Concept)
**Lab Address:** https://mail.pwnedbox.site/  
**HTTP Method:** GET  
**Vulnerable Parameters:** `page.id`, `page`, `id`, `cat`

**Payload:**
```html
"><ScRipT>\u0061lert("XSS")</ScRipT>
```

**Request Parameters:**
```http
?page.id="mihir%27"><ScRipT>\u0061lert("XSS1")</ScRipT>
?page="mihir%27"><ScRipT>\u0061lert("XSS")</ScRipT>
?id="mihir%27"><ScRipT>\u0061lert("XSS3")</ScRipT>
?cat="mihir%27"><ScRipT>\u0061lert("XSS4")</ScRipT>
```

**Observed Result:**

JavaScript execution was successfully triggered through all four tested GET parameters. The browser displayed alert dialogs containing `XSS1`, `XSS`, `XSS3`, and `XSS4`, respectively.

The x8 reflected-only scan also identified the following reflected parameters: `cat`, `fname`, `id`, `lname`, `number`, `page`, and `page.id`. However, successful JavaScript execution was confirmed only for the four parameters listed above.

**Screenshot Evidence:**
1. x8 output showing the reflected GET parameters.
<img width="1475" height="493" alt="image" src="https://github.com/user-attachments/assets/72836b33-615b-4dc5-b834-53643870661c" />

2. Reflected XSS execution through the `page.id` parameter (alert: `XSS1`).
<img width="1919" height="987" alt="image" src="https://github.com/user-attachments/assets/0547453f-eff3-4f7b-90d8-cebe7b09d822" />

3. Reflected XSS execution through the `page` parameter (alert: `XSS`).
<img width="1919" height="971" alt="image" src="https://github.com/user-attachments/assets/c4af9a42-b9fc-4a55-a2f6-380326224fd8" />


4. Reflected XSS execution through the `id` parameter (alert: `XSS3`).
<img width="1915" height="980" alt="image" src="https://github.com/user-attachments/assets/d5db1552-6c2b-4af8-b5ac-d5c675afe6ee" />

5. Reflected XSS execution through the `cat` parameter (alert: `XSS4`).
<img width="1919" height="1000" alt="image" src="https://github.com/user-attachments/assets/12688e86-d640-4f96-8c07-da87de642c85" />


## Impact
An attacker could craft a URL containing a malicious payload and trick a victim into opening it. Successful execution may allow:

- JavaScript execution in the victim's browser under the application's origin.
- Phishing or manipulation of page content.
- Unauthorized actions within the victim's session, depending on application security controls.
- Access to data available to scripts running in the affected page.

## Remediation
1. Apply context-aware output encoding to all user-controlled input before rendering it in HTML.
2. Avoid relying only on blacklist-based filtering for script tags or JavaScript keywords.
3. Validate and safely handle all GET parameters before processing or displaying them.
4. Implement a restrictive Content Security Policy (CSP) as an additional security layer.

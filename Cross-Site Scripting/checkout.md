## Title
Reflected XSS - Multi-Parameter Filter Evasion

## Description
A reflected Cross-Site Scripting (XSS) vulnerability was identified on the GET Parameter Reflection page of the target application. The application reflects user-controlled input from the `cat` parameter into the response without safely handling the injected HTML and JavaScript.

A crafted input using mixed-case HTML tags and a Unicode escape sequence successfully bypassed the filtering mechanism and executed JavaScript in the browser.

**Affected URL:** https://checkout.pwnedbox.site/  
**HTTP Method:** GET  
**Affected Parameter:** `cat`

## Steps to Reproduce
1. Open the following crafted URL in a browser:

   `https://checkout.pwnedbox.site/?cat=%27"><ScRiPt>\u0061lert(1)</ScRiPt>`

2. Allow the page to load.
3. Observe the JavaScript alert dialog displaying `1`.
4. Dismiss the alert and observe the reflected content on the page.

## POC (Proof of Concept)
**Lab Address:** https://checkout.pwnedbox.site/  
**HTTP Method:** GET  
**Vulnerable Parameter:** `cat`

**Payload:**
```html
'"><ScRiPt>\u0061lert(1)</ScRiPt>
```

**Request Parameter:**
```http
?cat=%27"><ScRiPt>\u0061lert(1)</ScRiPt>
```

**Observed Result:**

The injected JavaScript executed successfully and displayed a browser alert containing `1`. This confirms that the `cat` parameter is vulnerable to reflected XSS. The payload used mixed-case HTML tag syntax and a Unicode escape sequence for the `a` character in `alert`.

**Screenshot Evidence:**
1. Crafted URL loaded on the target page.
<img width="1919" height="977" alt="image" src="https://github.com/user-attachments/assets/f482dbf5-2718-498c-92da-66e636e5d191" />

2. JavaScript alert displaying `1`, confirming successful execution.
<img width="1919" height="978" alt="image" src="https://github.com/user-attachments/assets/2128ff62-6505-465a-83b7-5f4807f13ba8" />


## Impact
An attacker could craft a malicious URL and trick a victim into opening it. Successful execution of injected JavaScript could potentially allow:

- Execution of arbitrary JavaScript in the victim's browser context.
- Manipulation of page content and phishing attempts.
- Unauthorized actions within the victim's session, depending on application protections.

## Remediation
1. Apply context-aware output encoding to all user-controlled input before rendering it in HTML.
2. Avoid relying on blacklist-based filtering for HTML tags, attributes, or JavaScript patterns.
3. Validate input according to the expected format of the `cat` parameter.
4. Implement a restrictive Content Security Policy (CSP) as an additional security layer.

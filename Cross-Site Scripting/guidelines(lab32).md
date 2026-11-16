<img width="1919" height="983" alt="image" src="https://github.com/user-attachments/assets/0005ec48-64e2-4478-8139-92cbf7930892" />## Title
Reflected XSS - GIGW: Government Website Guidelines Portal

## Description
**Affected URL:** https://guidelines.pwnedbox.site/

The search functionality uses the `s` parameter to process user input. During testing, I found that the application does not properly handle the supplied input. A JavaScript payload injected through the search parameter was executed in the browser, indicating a Reflected Cross-Site Scripting (XSS) vulnerability.

## Steps to Reproduce
1. Open the lab website: https://guidelines.pwnedbox.site/
2. Locate the search functionality on the homepage.
3. Enter the following XSS payload into the search parameter `s`:

   `<ScRiPt>prompt("XSS");</ScRiPt>`

4. Submit the search request.
5. Observe the browser displaying a JavaScript prompt containing `XSS`.
6. After closing the prompt, observe the search results page.
7. Notice that the supplied search input is also reflected in the page content.

## POC (Proof of Concept)
**Affected Endpoint:** `/index.php`

**Affected Parameter:** `s`

**Payload:**
```html
<ScRiPt>prompt("XSS");</ScRiPt>
```
<img width="1916" height="993" alt="image" src="https://github.com/user-attachments/assets/b2eb0829-3299-4539-830b-59a6b1d4a588" />
<img width="1919" height="983" alt="image" src="https://github.com/user-attachments/assets/c03ea275-6766-4fae-986c-7e1fa53cd3b9" />
<img width="1919" height="1041" alt="image" src="https://github.com/user-attachments/assets/703a39b9-1d35-4dbb-91fa-b6cff63ec5ba" />


The injected JavaScript executed successfully and displayed a browser prompt containing `XSS`. The screenshots demonstrate the submitted search input, successful JavaScript execution, and reflected input on the search results page.

## Impact
1. An attacker could execute JavaScript in a victim's browser by sharing a specially crafted URL containing the malicious search parameter.
2. Depending on application security controls, this vulnerability could potentially enable phishing attacks, unauthorized actions, or exposure of accessible information.

## Remediation
1. Implement proper input validation and context-aware output encoding before displaying user-supplied search input.
2. Apply a Content Security Policy (CSP) to restrict unauthorized JavaScript execution and reduce the risk of XSS attacks.

## Title
Reflected XSS - RefSeek: Academic Search Portal

## Description
**Affected URL:** https://refseek.pwnedbox.site/

The search functionality uses the `q` parameter to process user input. During testing, I observed that the application does not properly handle certain user-supplied input. By injecting a JavaScript payload into the search parameter, I was able to execute JavaScript in the browser. This indicates a Reflected Cross-Site Scripting (XSS) vulnerability.

## Steps to Reproduce
1. Open the lab website: https://refseek.pwnedbox.site/
2. Locate the search functionality on the homepage.
3. Enter the following XSS payload into the `q` parameter:

   `<ScRiPt>prompt("XSS");</ScRiPt>`

4. Submit the search request.
5. Observe the browser displaying a JavaScript prompt containing `XSS`.
6. Close the prompt and observe the search results page.
7. Notice that the supplied search input is reflected in the search results.

## POC (Proof of Concept)
**Lab Address:** https://refseek.pwnedbox.site/

**Affected Endpoint:** `/index.php`

**Affected Parameter:** `q`

**Payload:**
```html
<ScRiPt>prompt("XSS");</ScRiPt>
```
<img width="1919" height="971" alt="image" src="https://github.com/user-attachments/assets/1556dc03-32f0-4a93-b21a-254ea37bd60c" />
<img width="1918" height="981" alt="image" src="https://github.com/user-attachments/assets/0025f83f-f1a1-44b4-b0f1-1e60b25fef46" />
<img width="1919" height="895" alt="image" src="https://github.com/user-attachments/assets/486b84f2-3468-45dd-88c0-be666ee722be" />


The injected JavaScript executed successfully and displayed a browser prompt containing `XSS`. The screenshots demonstrate the search input, successful JavaScript execution, and reflected input on the search results page.

## Impact
1. An attacker could execute JavaScript in a victim's browser through a specially crafted URL.
2. Depending on application security controls, this could potentially lead to phishing attacks, unauthorized actions, or exposure of accessible information.

## Remediation
1. Implement proper input validation and context-aware output encoding before displaying search input.
2. Apply a Content Security Policy (CSP) to restrict unauthorized JavaScript execution and reduce the risk of XSS attacks.

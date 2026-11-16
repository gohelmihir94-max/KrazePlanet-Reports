## Title
Reflected XSS - PubMed: National Library of Medicine Search Builder

## Description
**Affected URL:** https://pubmed.pwnedbox.site/

The PubMed Advanced Search Builder uses the `term` parameter to process search input. During testing, I found that the application does not properly handle certain user-supplied input. By injecting a JavaScript payload into the search parameter, I was able to execute JavaScript in the browser. This indicates a Reflected Cross-Site Scripting (XSS) vulnerability.

## Steps to Reproduce
1. Open the lab website: https://pubmed.pwnedbox.site/
2. Locate the PubMed Advanced Search Builder.
3. Enter the following XSS payload into the search input.
4. Submit the search request.
5. Observe the browser displaying a JavaScript prompt containing `XSS`.
6. Close the prompt and observe the search results page.
7. Notice that the supplied search input is reflected in the search results.

## POC (Proof of Concept)
**Lab Address:** https://pubmed.pwnedbox.site/

**Affected Endpoint:** `/index.php`

**Affected Parameter:** `term`

**Payload:**
```html
<ScRiPt>prompt("XSS");</ScRiPt>
```
<img width="1918" height="915" alt="image" src="https://github.com/user-attachments/assets/94bf4700-b13c-42f4-a39f-a01f29fcefc5" />
<img width="1919" height="979" alt="image" src="https://github.com/user-attachments/assets/0607b7d4-bfc3-4e13-9bcc-384ffb1ea24f" />
<img width="1919" height="909" alt="image" src="https://github.com/user-attachments/assets/9fbd8cb2-bfc7-43c2-8e6a-a468d9f9b913" />


The injected JavaScript executed successfully and displayed a browser prompt containing `XSS`. The screenshots demonstrate the injected search input, successful JavaScript execution, and reflected input on the search results page.

## Impact
1. An attacker could execute JavaScript in a victim's browser through a specially crafted URL.
2. Depending on application security controls, this could potentially lead to phishing attacks, unauthorized actions, or exposure of accessible information.

## Remediation
1. Implement proper input validation and context-aware output encoding before displaying user-supplied search terms.
2. Apply a Content Security Policy (CSP) to restrict unauthorized JavaScript execution and reduce the risk of XSS attacks.

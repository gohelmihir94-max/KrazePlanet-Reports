## Title
Reflected XSS - FreshCart: Grocery Delivery Search

## Description
**Affected URL:** https://freshcart.pwnedbox.site/

The FreshCart search functionality uses the `q` parameter to process user input. During testing, I observed that the application does not properly handle certain user-supplied input. By injecting a JavaScript payload into the search parameter, I was able to execute JavaScript in the browser. This indicates a Reflected Cross-Site Scripting (XSS) vulnerability.

## Steps to Reproduce
1. Open the lab website: https://freshcart.pwnedbox.site/
2. Locate the search functionality in the navigation bar.
3. Enter the following XSS payload into the search input:

   `<ScRiPt>prompt("XSS");</ScRiPt>`

4. Submit the search request.
5. Observe the browser displaying a JavaScript prompt containing `XSS`.
6. Close the prompt and observe the search results page.
7. Notice that the supplied search input is reflected in the results page.
8. Verify that the affected parameter in the URL is `q`.

## POC (Proof of Concept)
**Lab Address:** https://freshcart.pwnedbox.site/

**Affected Endpoint:** `/index.php`

**Affected Parameter:** `q`

**Payload:**
```html
<ScRiPt>prompt("XSS");</ScRiPt>
```
<img width="1913" height="966" alt="image" src="https://github.com/user-attachments/assets/1ebb4a8b-82c5-4e05-b8b9-0ae1b79c5ecf" />
<img width="1919" height="980" alt="image" src="https://github.com/user-attachments/assets/159035a2-f6fb-4973-8d87-bb19ac744fef" />
<img width="1913" height="976" alt="image" src="https://github.com/user-attachments/assets/34775070-0865-431c-8f37-219f31412c4d" />


The injected JavaScript executed successfully and displayed a browser prompt containing `XSS`. The screenshots demonstrate the original search input, successful JavaScript execution, and reflected input on the search results page. This confirms that the search functionality processes the supplied input without proper output handling.

## Impact
1. An attacker could execute JavaScript in a victim's browser through a specially crafted URL.
2. Depending on application security controls, this could potentially enable phishing attacks, unauthorized actions, or exposure of accessible information.

## Remediation
1. Implement proper input validation and context-aware output encoding before displaying user-supplied search terms.
2. Apply a Content Security Policy (CSP) to restrict unauthorized JavaScript execution and reduce the risk of XSS attacks.

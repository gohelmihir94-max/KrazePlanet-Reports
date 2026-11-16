## Title
Reflected XSS - TutorialRepublic: Web Development Reference Search

## Description
**Affected URL:** https://tutorialrepublic.pwnedbox.site/

The search parameter `q` allows reflected JavaScript execution in the browser.

## Steps to Reproduce
1. Open the affected website.
2. Inject the XSS payload into the `q` parameter.
3. Load the crafted URL.
4. Observe the JavaScript prompt displaying `XSS`.

## POC (Proof of Concept)
**Payload:**
```html
<ScRiPt>prompt("XSS");</ScRiPt>
```
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/19e1d73f-804f-4115-8cad-ed11a1b76fc4" />
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/192960dc-3fa8-49fa-91cd-f3f21fb7adf5" />


JavaScript prompt successfully executed in the browser.

## Impact
1. An attacker could execute JavaScript in a victim's browser through a crafted URL.
2. This could lead to phishing attacks, unauthorized actions, or theft of accessible session data, depending on browser and application protections.

## Remediation
1. Implement proper input validation and context-aware output encoding.
2. Apply a Content Security Policy (CSP) to restrict the execution of unauthorized scripts.

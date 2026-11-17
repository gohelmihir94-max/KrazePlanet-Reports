## Title
Reflected XSS - Reddit

## Description
The application is vulnerable to Reflected Cross-Site Scripting (XSS) through the `post_id` parameter on the widgets page. The application reflects user-controlled input into the page without properly sanitizing it. By injecting an HTML event handler (`onmouseover`), an attacker can execute JavaScript when a user hovers over the affected button.

**Affected URL:** https://widgets.pwnedbox.site/

## Steps to Reproduce
1. Open the crafted URL provided in the POC.
2. Wait for the page to load.
3. Hover over the affected button.
4. Observe the JavaScript alert displaying the website's domain.

## POC (Proof of Concept)
**Lab Address:** https://widgets.pwnedbox.site/

**HTTP Method:** GET

**Vulnerable Parameter:** `post_id`

**Payload:**
```html
t3_u9po1l onmouseover=alert(document.domain) y=
```

**Crafted URL:**
```text
https://widgets.pwnedbox.site/?post_id=t3_u9po1l%20onmouseover=alert(document.domain)%20y=
```

**Observed Result:**

When the crafted URL is opened, the injected event handler is reflected in the page. Hovering over the affected button triggers JavaScript execution and displays an alert containing `widgets.pwnedbox.site`, confirming the reflected XSS vulnerability.

**Screenshot Evidence:**
1. Crafted URL containing the injected `onmouseover` payload.
<img width="1915" height="968" alt="image" src="https://github.com/user-attachments/assets/1ec20ac7-330a-460f-921c-45b4b8536966" />

2. Reddit-style page after loading the crafted URL.
<img width="1919" height="935" alt="image" src="https://github.com/user-attachments/assets/4a1134fe-69f9-4f58-8b4e-a930fdaa6853" />


3. Browser alert displaying `widgets.pwnedbox.site` after hovering over the affected button.
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6e35ef85-be59-4b6a-b37a-0c2092f943e5" />

## Impact
An attacker could share a crafted URL with a victim and execute JavaScript in the context of the vulnerable website when the victim interacts with the affected element.

- JavaScript execution in the victim's browser.
- Phishing or manipulation of displayed page content.
- Potential unauthorized actions within the victim's authenticated session, depending on application protections.

## Remediation
1. Apply context-aware output encoding to user-controlled input.
2. Prevent untrusted input from being inserted into HTML attributes or event-handler contexts.
3. Validate the `post_id` parameter using an appropriate allowlist.
4. Avoid relying only on blacklist-based filtering.
5. Implement a restrictive Content Security Policy (CSP).

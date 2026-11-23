## Title
Stored Cross-Site Scripting (XSS) in Movie Viewer Reactions

## Description
The movie details page allows users to submit reactions and comments. The "Share Your Thoughts & Reactions" field accepts HTML input without properly sanitizing it.

An attacker can submit an HTML payload containing an event handler. The payload is stored and executes JavaScript when the movie page is viewed again.

**Affected URL:** https://netflix.pwnedbox.site/101

**HTTP Method:** POST

**Affected Field:** Share Your Thoughts & Reactions

## Steps to Reproduce
1. Open the movie details page.
2. Enter the XSS payload in the "Share Your Thoughts & Reactions" field.
3. Click **Post Reaction & Feeling**.
4. Reload or revisit the movie page.
5. Observe the JavaScript prompt.

## POC (Proof of Concept)
**Lab Address:** https://netflix.pwnedbox.site/101

**HTTP Method:** POST

**Vulnerable Field:** Share Your Thoughts & Reactions

**Payload:**
```html
<img src=x onerror=prompt(1)>
```

**Request Parameter:**

The exact backend parameter name and raw HTTP request were not captured in the screenshots. The payload was submitted through the reaction/comment field.

**Observed Result:**

After submitting the payload, it was stored as a viewer reaction. When the movie page was loaded again, the browser displayed a JavaScript prompt containing `1`, confirming stored XSS in the reaction/comment content.

**Screenshot Evidence:**
1. Reaction form showing the injected payload in the "Share Your Thoughts & Reactions" field.
<img width="1919" height="983" alt="image" src="https://github.com/user-attachments/assets/f79b4ea5-e5c2-4c65-82e5-7d0c3bf93207" />

2. Browser prompt displaying `1` after the page was loaded again.
<img width="1919" height="982" alt="image" src="https://github.com/user-attachments/assets/d290fd66-4a86-4b8b-86c8-16b3951217b2" />

## Impact
An attacker could submit malicious content that executes JavaScript in the browser of users viewing the affected movie page.

- Execute arbitrary JavaScript in the application's origin.
- Display misleading content or phishing interfaces.
- Modify the visible movie page or reaction content.
- Potentially perform actions using a victim's authenticated session, depending on application security controls.

## Remediation
1. Apply context-aware output encoding to user-submitted reaction content.
2. Sanitize HTML input before storing or rendering it.
3. Avoid inserting untrusted content into the page as raw HTML.
4. Implement a restrictive Content Security Policy (CSP) as an additional security layer.

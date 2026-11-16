## Title
Reflected XSS - KrazePlanetLabs: GET Parameter Reflection

## Description
**Affected URL:** https://search.pwnedbox.site/

The application contains a form with First Name and Last Name input fields. During testing, I discovered that the application does not properly handle user-supplied input passed through GET parameters. JavaScript payloads injected into the `fname` and `lname` parameters were executed in the browser. This indicates a Reflected Cross-Site Scripting (XSS) vulnerability affecting both input parameters.

## Steps to Reproduce
1. Open the lab website: https://search.pwnedbox.site/
2. Locate the First Name and Last Name input fields.
3. Enter the following payload in the First Name field:

   `<ScRiPt>prompt("XSS1");</ScRiPt>`

4. Enter the following payload in the Last Name field:

   `<ScRiPt>prompt("XSS2");</ScRiPt>`

5. Submit the form.
6. Observe the first JavaScript prompt displaying `XSS1`.
7. Close the first prompt and observe the second prompt displaying `XSS2`.
8. Check the browser URL and observe that both values are passed through GET parameters named `fname` and `lname`.

## POC (Proof of Concept)
**Lab Address:** https://search.pwnedbox.site/

**Affected Endpoint:** `/`

**HTTP Method:** GET

**Affected Parameters:**
- `fname` – First Name
- `lname` – Last Name

**First Name Payload:**
```html
<ScRiPt>prompt("XSS1");</ScRiPt>
```

**Last Name Payload:**
```html
<ScRiPt>prompt("XSS2");</ScRiPt>
```

**Tested URL:**
```text
https://search.pwnedbox.site/?fname=%22mihir%27%22%3C%3E%3CScRipT%3Eprompt%28%22XSS1%22%29%3B%3C%2FScRipT%3E&lname=%22mihir%27%22%3C%3E%3CScRipT%3Eprompt%28%22XSS2%22%29%3B%3C%2FScRipT%3E
```

<img width="1902" height="978" alt="image" src="https://github.com/user-attachments/assets/fc004975-9778-4ed6-9bda-c36e8c08b35d" />

<img width="1910" height="978" alt="image" src="https://github.com/user-attachments/assets/21a0d04d-a83e-4d08-8d67-8205f2710c9c" />

<img width="1919" height="998" alt="image" src="https://github.com/user-attachments/assets/dbf4da80-6f59-4ef7-82fe-8f04bedbd528" />

<img width="1908" height="933" alt="image" src="https://github.com/user-attachments/assets/b8032a2f-af0e-4e3b-9df0-f780d78fdf70" />


Both injected JavaScript payloads executed successfully. The browser displayed two separate prompts containing `XSS1` and `XSS2`. The screenshots demonstrate successful JavaScript execution through both GET parameters and the resulting page behaviour.

## Impact
1. An attacker could execute JavaScript in a victim's browser by sending a specially crafted URL containing malicious GET parameter values.
2. Depending on application security controls, this could potentially enable phishing attacks, unauthorized actions, or exposure of accessible information.

## Remediation
1. Implement proper input validation and context-aware output encoding for both `fname` and `lname` parameters.
2. Apply a Content Security Policy (CSP) to restrict unauthorized JavaScript execution and reduce the risk of XSS attacks.

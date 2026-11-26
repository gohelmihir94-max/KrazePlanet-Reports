## Title
Self XSS - POST XSS in Input Tag Value

## Description
**Affected URL:** https://survey.pwnedbox.site/

The Name input accepts user-controlled data that is processed and rendered by the application. A crafted value containing HTML and a script tag executes JavaScript when the form is submitted.

The demonstrated execution occurs in the same browser session, so this is classified as Self XSS.

## Steps to Reproduce
1. Open https://survey.pwnedbox.site/.
2. Enter the following payload in the Name field:
   `mihir""><><ScRiPt>\u0061lert('XSS')</ScRiPt>`
3. Click **Enter**.
4. Observe the JavaScript alert displaying `XSS`.

## POC (Proof of Concept)
**Lab Address:** https://survey.pwnedbox.site/

**HTTP Method:** POST (as indicated by the lab title; the request method is not visible in the screenshots).

**Vulnerable Parameter:** Name

**Payload:**
```html
mihir""><><ScRiPt>\u0061lert('XSS')</ScRiPt>
```

**Observed Result:**

After submitting the Name field, the browser executes the injected JavaScript and displays an alert containing `XSS`.

**Screenshot Evidence:**
1. Name field populated with the crafted XSS payload.
<img width="1919" height="971" alt="image" src="https://github.com/user-attachments/assets/4460ca88-f183-455a-b3ff-dc78035b99c3" />

2. Browser alert displaying `XSS`, confirming JavaScript execution.
 <img width="1918" height="976" alt="image" src="https://github.com/user-attachments/assets/4f0b5024-9853-4550-a314-1bc07a3bf556" />


## Impact
This is Self XSS because the demonstrated execution requires the user to submit the crafted input themselves.

- JavaScript execution in the submitting user's browser.
- Potential manipulation of the user's own page content.
- No impact on other users or account compromise has been demonstrated.

## Remediation
1. Apply context-aware output encoding when displaying user-supplied values.
2. Safely encode special characters before inserting data into HTML attributes.
3. Validate and sanitize input on the server side.
4. Avoid inserting untrusted input directly into HTML.

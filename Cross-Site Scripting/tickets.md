## Title

Reflected XSS - Event Handler Filter

## Description

A Reflected Cross-Site Scripting (XSS) vulnerability was identified in the GET Parameter Reflection functionality on `tickets.pwnedbox.site`.

During testing, the application was found to accept user-controlled input through the `lname` (Last Name) GET parameter.

The application implements filtering mechanisms to prevent the injection of HTML event handlers. However, a specially crafted payload using the `<body onpageshow>` event handler successfully bypassed the filtering mechanism.

The injected event handler executed JavaScript when the page loaded. Successful JavaScript execution was confirmed through a browser alert displaying `2`.

**Affected URL:** [https://tickets.pwnedbox.site/](https://tickets.pwnedbox.site/)

**HTTP Method:** GET

**Affected Parameter:** `lname`

## Steps to Reproduce

1. Open `https://tickets.pwnedbox.site/` and navigate to the GET Parameter Reflection page.
2. Leave the First Name field empty and enter the following payload in the Last Name field:

   ```html
   "><body onpageshow=/*x*/this['a'+'l'+'e'+'r'+'t'](2)//>
   ```

3. Click the Submit button.
4. Observe the JavaScript alert displaying `2`.

## POC (Proof of Concept)

**Lab Address:** [https://tickets.pwnedbox.site/](https://tickets.pwnedbox.site/)

**HTTP Method:** GET

**Vulnerable Parameter:** `lname`

**Payload:**

```html
"><body onpageshow=/*x*/this['a'+'l'+'e'+'r'+'t'](2)//>
```

**Request Parameter:**

```http
?fname=&lname="><body onpageshow=/*x*/this['a'+'l'+'e'+'r'+'t'](2)//>
```

**Observed Result:**

The application processed the supplied payload through the `lname` parameter. The injected `<body>` element and its `onpageshow` event handler successfully executed JavaScript despite the application's event-handler filtering mechanism.

A browser alert displaying `2` appeared, confirming successful JavaScript execution.

**Screenshot Evidence:**

1. Screenshot showing the injected payload in the Last Name field.
<img width="1919" height="979" alt="image" src="https://github.com/user-attachments/assets/4171c556-78f0-4d26-8c49-d6fea36cad09" />

2. Screenshot showing the browser alert displaying `2`.
<img width="1913" height="980" alt="image" src="https://github.com/user-attachments/assets/a70a63cb-1cfa-4534-a3ad-844f0e4c3a6a" />

3. Screenshot showing the resulting page after dismissing the alert.
<img width="1916" height="979" alt="image" src="https://github.com/user-attachments/assets/5db5a0ea-f7b4-4179-a3d7-7b04f0d6ad23" />

## Impact

An attacker could potentially craft a malicious URL containing an XSS payload and trick a victim into opening it.

Successful exploitation could result in:

- Execution of arbitrary JavaScript in the victim's browser.
- Phishing attacks through manipulated page content.
- Unauthorized modification of displayed application content.
- Potential abuse of the victim's authenticated session, depending on application security protections.

## Remediation

3. Prevent injection of untrusted HTML elements and event-handler attributes.
4. Validate and safely handle user-supplied values in the `fname` and `lname` parameters.
5. Implement a restrictive Content Security Policy (CSP) as an additional security layer.

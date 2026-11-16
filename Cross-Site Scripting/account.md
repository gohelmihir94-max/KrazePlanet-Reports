## Title

Reflected XSS - Extended Function Filter

## Description

A Reflected Cross-Site Scripting (XSS) vulnerability was identified in the GET Parameter Reflection functionality on `account.pwnedbox.site`.

During testing, the application was found to accept user-controlled input through the `lname` (Last Name) GET parameter.

The application implements function-name filtering to prevent JavaScript execution. However, a specially crafted HTML event-handler payload using the `<body onpageshow>` event and JavaScript string concatenation successfully bypassed the filtering mechanism.

Successful JavaScript execution was confirmed through a browser alert displaying `1`.

**Affected URL:** https://account.pwnedbox.site/

**HTTP Method:** GET

**Affected Parameter:** `lname`

## Steps to Reproduce

1. Open the lab address: `https://account.pwnedbox.site/`
2. Navigate to the GET Parameter Reflection page.
3. Leave the First Name field empty.
4. Enter the following payload in the Last Name field:

   ```html
   "><body onpageshow=/*x*/this['a'+'l'+'e'+'r'+'t'](1)//>
   ```

5. Click the Submit button.
6. Observe that the injected HTML event handler is processed.
7. A JavaScript alert displaying `1` appears.
8. Dismiss the alert and observe the resulting page.

## POC (Proof of Concept)

**Lab Address:** https://account.pwnedbox.site/

**HTTP Method:** GET

**Vulnerable Parameter:** `lname`

**Payload:**

```html
"><body onpageshow=/*x*/this['a'+'l'+'e'+'r'+'t'](1)//>
```

**Request Parameter:**

```http
?fname=&lname="><body onpageshow=/*x*/this['a'+'l'+'e'+'r'+'t'](1)//>
```

**Observed Result:**

The application processed the supplied payload through the `lname` parameter. The injected `onpageshow` event handler successfully executed JavaScript despite the function-name filtering mechanism.

A browser alert displaying `1` appeared, confirming successful JavaScript execution.

**Screenshot Evidence:**

1. Screenshot showing the injected payload in the Last Name field.
<img width="1919" height="960" alt="image" src="https://github.com/user-attachments/assets/b5918b99-66dd-4ccb-ba02-df343eb39ab1" />

2. Screenshot showing the browser alert displaying `1`.
<img width="1913" height="985" alt="image" src="https://github.com/user-attachments/assets/2a736254-54df-4f22-9406-ab10e13d52ef" />

3. Screenshot showing the resulting page after dismissing the alert.
<img width="1919" height="926" alt="image" src="https://github.com/user-attachments/assets/3d082bea-1898-4c6c-bb4b-70ce33c7058b" />


## Impact

An attacker could potentially craft a malicious URL containing an XSS payload and trick a victim into opening it.

Successful exploitation could result in:

- Execution of arbitrary JavaScript in the victim's browser.
- Phishing attacks through manipulated page content.
- Unauthorized modification of displayed application content.
- Potential abuse of the victim's authenticated session, depending on application security protections.

## Remediation

1. Implement context-aware output encoding for all user-controlled input before rendering it in HTML.
2. Avoid relying solely on function-name filtering to prevent JavaScript execution.
3. Prevent injection of untrusted HTML elements and event-handler attributes.
4. Validate and safely handle user-supplied values in the `fname` and `lname` parameters.
5. Implement a restrictive Content Security Policy (CSP) as an additional security layer.

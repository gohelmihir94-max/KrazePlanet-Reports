## Title

Stored XSS - Article Content

## Description

A Stored Cross-Site Scripting (XSS) vulnerability was identified in the article publishing functionality of the Quill application hosted at `cms.pwnedbox.site`.

The application allows an authenticated user to create and publish articles containing user-controlled content. During testing, an HTML image element with an `onerror` event handler was submitted in the article description. The application stored the supplied content and rendered it as HTML on the My Articles page.

Because the content was rendered without adequate sanitization, the injected JavaScript executed when the published article content was loaded.

Successful JavaScript execution was confirmed through a browser dialog displaying `1`.

**Affected URL:** https://cms.pwnedbox.site/

**Vulnerability Type:** Stored Cross-Site Scripting (XSS)

**Affected Functionality:** Article creation and publishing

**Affected Input:** Article Description

## Steps to Reproduce

1. Log in to the application at `https://cms.pwnedbox.site/`.
2. Navigate to the Write section and create a new article.
3. Enter the following payload in the Description field:

   ```html
   "mihir'"><ImG src=x onerror=confirm(1)>
   ```

4. Publish the article.
5. Open the My Articles page and observe the JavaScript confirmation dialog.

## POC (Proof of Concept)

**Lab Address:** https://cms.pwnedbox.site/

**HTTP Method:** Not captured in the provided screenshots.

**Vulnerable Input:** Article Description

**Payload:**

```html
"mihir'"><ImG src=x onerror=confirm(1)>
```

**Execution Mechanism:**

The injected image element uses an invalid image source (`src=x`). When the browser fails to load the image, its `onerror` event handler executes the JavaScript confirmation function.

**Observed Result:**

The article was successfully published with the injected HTML content. When the My Articles page loaded, the browser displayed a JavaScript confirmation dialog containing `1`.

The screenshot also shows the application notification:

`Article published — XSS payload fires when your articles page loads.`

This confirms that the payload was stored and executed during subsequent page rendering.

**Screenshot Evidence:**

1. Article creation page showing the injected payload in the Description field.
<img width="1917" height="906" alt="image" src="https://github.com/user-attachments/assets/a9d3f11c-4b7d-433c-81db-25d2de271112" />

2. My Articles page showing the JavaScript confirmation dialog.
<img width="1919" height="977" alt="image" src="https://github.com/user-attachments/assets/8270e165-23fa-40d6-863d-c04d971634cb" />


## Impact
It allows attackers to hijack user session
It potentially leads to full account takeover
It allows to perform unauthorized actions within the vulnerable application

## Remediation

Filter out HTML tags like: <script>, <img>, <svg> from the Report Name field before saving anything to the database
Filter out JavaScript methods like: alert(), confirm(), prompt() so even if a tag slips through the method won't execute
If you're using PHP then use htmlspecialchars() function before rendering any user input back to the page
Use Cloudflare as they have so many WAF rules that almost all XSS payloads will be blocked automatically before even reaching the server

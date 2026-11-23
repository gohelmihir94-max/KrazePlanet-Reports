## Title

Stored XSS - Community Events Functionality

## Description

A Stored Cross-Site Scripting (XSS) vulnerability was identified in the Community Events functionality on `articles.pwnedbox.site`.

During testing, the application accepted user-controlled input through the Event Title, Event Date & Time, and Event Description fields. HTML payloads containing image tags and JavaScript event handlers were submitted while creating a community event.

The application successfully saved and displayed the supplied content in the Scheduled Events section. JavaScript execution was confirmed through a browser prompt displaying `3`, demonstrating that the stored payload was executed when the page was loaded.

**Affected URL:** https://articles.pwnedbox.site/index.php

**HTTP Method:** POST (event creation)

**Affected Parameters:** Event Title, Event Date & Time, Event Description (exact parameter names not visible in the screenshots)

## Steps to Reproduce

1. Open `https://articles.pwnedbox.site/index.php`.
2. Navigate to the Community Events section.
3. Enter the following payloads into the event fields:

   **Event Title:**
   ```html
   ""><img src=x onerror=prompt(1)>
   ```

   **Event Date & Time:**
   ```html
   ""><img src=x onerror=prompt(2)>
   ```

   **Event Description:**
   ```html
   ""><img src=x onerror=prompt(3)>
   ```

4. Click **Publish Event**.
5. Observe the stored event in the Scheduled Events section and the JavaScript prompt.

## POC (Proof of Concept)

**Lab Address:** https://articles.pwnedbox.site/index.php

**HTTP Method:** POST

**Vulnerable Inputs:** Event Title, Event Date & Time, Event Description

**Payloads:**

```html
""><img src=x onerror=prompt(1)>
""><img src=x onerror=prompt(2)>
""><img src=x onerror=prompt(3)>
```

**Request Parameter:**

The event creation request is submitted using POST. The exact parameter names and complete request body were not captured in the supplied screenshots. Capture the request in Burp Suite to document the complete HTTP request.

**Crafted URL:**

```text
https://articles.pwnedbox.site/index.php
```

The screenshots demonstrate stored payload execution through the application page. They do not show a standalone crafted GET URL containing the payload.

**Observed Result:**

The application displayed a success message confirming that the community event was created. The submitted payloads were rendered in the Scheduled Events section.

A browser prompt displaying `3` appeared, confirming JavaScript execution from the stored event content.

**Screenshot Evidence:**
1. Event creation form containing the injected payloads.
<img width="1919" height="972" alt="image" src="https://github.com/user-attachments/assets/3d680dd7-a3a6-4b7e-9141-33b391a632a0" />

2. Browser prompt displaying `1`.
<img width="1914" height="983" alt="image" src="https://github.com/user-attachments/assets/836bcf4f-62f7-43f4-a4d9-6a860c06da9d" />


3. Browser prompt displaying `3`.
<img width="1919" height="979" alt="image" src="https://github.com/user-attachments/assets/234e4ed0-9a83-4df2-996a-6a1b26769445" />


## Impact

- It allows attackers to hijack user session
- It potentially leads to full account takeover
- It allows to perform unauthorized actions within the vulnerable application

## Remediation

- Filter out HTML tags like: `<script>`, `<img>`, `<svg>` from the Report Name field before saving anything to the database
- Filter out JavaScript methods like: `alert()`, `confirm()`, `prompt()` so even if a tag slips through the method won't execute
- If you're using PHP then use `htmlspecialchars()` function before rendering any user input back to the page
- Use Cloudflare as they have so many WAF rules that almost all XSS payloads will be blocked automatically before even reaching the server

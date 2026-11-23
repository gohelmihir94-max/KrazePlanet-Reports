## Title
Stored XSS in Network Report Name

## Description
The AdPulse application at `https://ads.pwnedbox.site` is vulnerable to Stored Cross-Site Scripting (XSS) through the **Report Name** field in the New Network Report form.

The application accepts HTML containing an event handler in the Report Name field and stores it without properly sanitizing or encoding the input. After the report is saved, the injected JavaScript executes when the reports page is rendered.

**Affected URL:** https://ads.pwnedbox.site  
**HTTP Method:** POST (report submission)  
**Affected Parameter:** Report Name

## Steps to Reproduce
1. Log in to the AdPulse application and navigate to the Reports page.
2. Click **New Network Report**.
3. Enter the following payload in the Report Name field:
   ```html
   <img src=x onerror=prompt(1)>
   ```
4. Complete the remaining required fields and click **Run & Save Report**.
5. Observe the JavaScript prompt after the report is saved.

## POC (Proof of Concept)
**Lab Address:** https://ads.pwnedbox.site  
**HTTP Method:** POST  
**Vulnerable Parameter:** Report Name

**Payload:**
```html
<img src=x onerror=prompt(1)>
```

**Request Parameter:**
```http
Report Name=<img src=x onerror=prompt(1)>
```

**Observed Result:**

After submitting the report, the application saved the report and executed the injected JavaScript, displaying a browser prompt containing `1`. The application also displayed a confirmation message indicating that the saved XSS payload would execute when someone views the page.

**Screenshot Evidence:**
1. New Network Report form showing the XSS payload entered in the Report Name field.
<img width="1919" height="1021" alt="image" src="https://github.com/user-attachments/assets/a8229875-302f-4b4a-94e7-8daeb3d17791" />


2. Browser prompt displaying `1` after the report was saved.
<img width="1919" height="1027" alt="image" src="https://github.com/user-attachments/assets/4c9ea21c-bf26-4ebb-a13b-2f2e9928798e" />


3. Reports listing showing the saved report and the application's confirmation message.
<img width="1918" height="985" alt="image" src="https://github.com/user-attachments/assets/44ec1082-4fa8-419f-95ca-bf70eec12231" />


## Impact
This vulnerability allows an attacker to store malicious JavaScript in a report name. When another user views the affected page, the script may execute in that user's browser session.

Potential impact includes:
- Execution of attacker-controlled JavaScript in another user's browser.
- Phishing or manipulation of application content.
- Unauthorized actions performed within the victim's authenticated session, depending on application protections.
- Potential exposure of accessible page data.

## Remediation
1. Apply context-aware output encoding to all user-controlled data before displaying it.
2. Sanitize user input and prevent untrusted HTML or event-handler attributes from being rendered.
3. Use a restrictive Content Security Policy (CSP) to reduce the impact of XSS.
4. Validate and safely handle the Report Name field on the server side.

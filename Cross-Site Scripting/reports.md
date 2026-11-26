## Title
Self XSS - in Document Title

## Description
**Affected URL:** https://reports.pwnedbox.site/

The Name input accepts crafted HTML content that is processed by the application. When the submitted value is interpreted in the page's document-title context, the injected HTML breaks out of the expected context and triggers JavaScript execution.

The demonstrated execution occurs in the submitting user's own browser, so this is classified as Self XSS.

## Steps to Reproduce
1. Open https://reports.pwnedbox.site/.
2. Enter the following payload in the Name field:
   ```html
   </Title/</h1/</h2/</h3/</h4/</div/</p/</a/</font/</label/</button/</img/</ul/</ol/</li/</option/</span/</Style/</Script/</textArea/</iFrame/</noScript><Img Src=OnXSS OnError=(confirm)(1)>
   ```
3. Click **Enter**.
4. Observe the JavaScript confirmation dialog displaying `1`.

## POC (Proof of Concept)
**Lab Address:** https://reports.pwnedbox.site/

**Vulnerable Input:** Name

**Payload:**
```html
</Title/</h1/</h2/</h3/</h4/</div/</p/</a/</font/</label/</button/</img/</ul/</ol/</li/</option/</span/</Style/</Script/</textArea/</iFrame/</noScript><Img Src=OnXSS OnError=(confirm)(1)>
```

**Observed Result:**

After submitting the payload, the browser displays a JavaScript confirmation dialog containing `1`, demonstrating successful script execution.

**Screenshot Evidence:**
1. Name field populated with the crafted HTML payload.
<img width="1919" height="977" alt="image" src="https://github.com/user-attachments/assets/42ee10c6-7f55-4713-901a-22d5b85eff0e" />

2. Browser confirmation dialog displaying `1`.
<img width="1919" height="978" alt="image" src="https://github.com/user-attachments/assets/dde8010a-8df0-4859-8518-ebd6fca82364" />

## Impact
This is Self XSS because the demonstrated execution requires the user to submit the crafted input themselves.

- JavaScript execution in the submitting user's browser.
- Potential manipulation of the user's page content.
- No execution against another user's session or account compromise has been demonstrated.

## Remediation
1. Apply context-aware output encoding when rendering user-controlled values.
2. Safely encode special characters before inserting data into HTML or document-title contexts.
3. Validate input on the server side.
4. Avoid inserting untrusted input directly into HTML or document metadata.

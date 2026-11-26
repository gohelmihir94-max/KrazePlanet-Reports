## Title
Self XSS - via POST Parameter

## Description
**Affected URL:** https://forms.pwnedbox.site/

The search functionality accepts user-controlled input and renders the submitted value in the search results area without proper output encoding. A crafted HTML payload executes JavaScript in the same browser session, confirming XSS.

The demonstrated behavior is classified as Self XSS because execution was triggered through the user's own input.

## Steps to Reproduce
1. Open https://forms.pwnedbox.site/.
2. Enter the following payload in the search field:
   `"><img src=x onerror=alert(1)>`
3. Click **Search**.
4. Observe the JavaScript alert displaying `1`.

## POC (Proof of Concept)
**Lab Address:** https://forms.pwnedbox.site/

**HTTP Method:** POST (as identified by the lab title; not independently verified in the screenshots).

**Vulnerable Input:** Search field.

**Payload:**
```html
"><img src=x onerror=alert(1)>
```

**Observed Result:**

The submitted input is reflected in the search results area. The injected image element triggers its `onerror` event, executing JavaScript and displaying an alert containing `1`.

**Screenshot Evidence:**
1. Search field containing the XSS payload and reflected search result.
<img width="1919" height="967" alt="image" src="https://github.com/user-attachments/assets/edd2dae3-53d5-4202-8d35-be6e2baf0578" />

2. Browser alert displaying `1`, confirming JavaScript execution.
<img width="1919" height="971" alt="image" src="https://github.com/user-attachments/assets/abeada29-5dec-4940-ac52-c260b5708017" />


## Impact
This is a Self XSS finding because the demonstrated execution requires the user to submit the payload themselves.

- JavaScript execution in the submitting user's browser.
- Potential manipulation of the user's own page content.
- No impact on other users or account compromise has been demonstrated.

## Remediation
1. Apply context-aware output encoding to user-controlled search input.
2. Treat search terms as plain text and never insert them as raw HTML.
3. Validate and safely handle submitted input on the server and client sides.

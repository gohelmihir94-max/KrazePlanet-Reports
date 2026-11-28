## Title
Open Redirect - Semrush

## Description
**Affected URL:** https://out.pwnedbox.site/

The application is vulnerable to an Open Redirect through the `url` parameter in `index.php`. The parameter accepts an external URL, allowing an attacker to supply a destination such as `https://bing.com`. The application responds with an HTTP 302 redirect and sends the browser to the supplied external website.

## Steps to Reproduce
1. Open `https://out.pwnedbox.site/index.php?tab=keywords`.
2. Click the **Visit Page** button for any keyword entry.
3. Intercept the outgoing request in Burp Suite and send it to Intruder.
4. Set the `url` parameter to `https://bing.com` and send the request.
5. Observe the HTTP 302 response and open the resulting URL in a browser to verify the redirect.

## POC (Proof of Concept)

**Lab Address:** https://out.pwnedbox.site/

**HTTP Method:** GET

**Vulnerable Parameter:** `url`

**Payload:**
```text
https://bing.com
```

**Request:**
```http
GET /index.php?url=https%3a%2f%2fbing%2ecom HTTP/2
Host: out.pwnedbox.site
```

**Observed Response:**
```http
HTTP/2 302 Found
Location: https://bing.com
```

The server returns an HTTP 302 response with the external destination in the `Location` header. Opening the crafted URL in the browser redirects the user to Bing.

**Screenshot Evidence:**
1. Semrush dashboard showing the **Visit Page** link.
<img width="1919" height="976" alt="image" src="https://github.com/user-attachments/assets/1152ee94-371a-4c49-94e4-4e24a2c75ba1" />

2. Burp Intruder configuration showing the `url` parameter selected as the payload position and the redirect payload list.
<img width="1918" height="960" alt="image" src="https://github.com/user-attachments/assets/7f967c57-f983-4565-9419-7d074f38cf8f" />

3. Intruder results showing HTTP 302 responses for external URL payloads.
<img width="1835" height="984" alt="image" src="https://github.com/user-attachments/assets/aff662bd-4126-440b-a88e-375b5233f4c1" />

4.  Browser showing the crafted URL followed by the successful redirect to Bing.
<img width="1919" height="978" alt="image" src="https://github.com/user-attachments/assets/93461a98-521a-4b42-b07d-2e98dd8ce483" />
<img width="1915" height="981" alt="image" src="https://github.com/user-attachments/assets/1fd64251-591e-414a-ad89-179a53681b43" />



## Impact

- It allows attackers to redirect users from a trusted application domain to malicious or phishing websites.
- It may lead to credential theft or sensitive information disclosure through convincing phishing pages.
- It can be abused to distribute malicious links that appear to originate from a trusted domain.
- It may damage the application's reputation and user trust.

## Remediation

1. Validate redirect destinations against a strict allowlist of trusted domains.
2. Reject arbitrary external URLs supplied through the `url` parameter.
3. Use predefined internal paths or relative URLs instead of user-controlled redirect destinations.
4. Normalize and validate URLs before redirecting, and use a safe default destination when validation fails.

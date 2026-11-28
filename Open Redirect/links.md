## Title
Open Redirect - Omise

## Description
**Affected URL:** https://links.pwnedbox.site/

The application is vulnerable to an Open Redirect through URL path manipulation. By supplying an external hostname after multiple forward slashes, an attacker can cause the application to respond with an HTTP 302 redirect to an external website.

This allows attackers to use the trusted `links.pwnedbox.site` domain to redirect users to potentially malicious or phishing websites.

**HTTP Method:** GET

**Affected Endpoint:** `/`

## Steps to Reproduce
1. Open the Omise Developer Blog at `https://links.pwnedbox.site/`.
2. Intercept the request in Burp Suite and send it to Intruder.
3. Modify the request path to the following:
   `////bing.com/index.php?category=developers&page=1`
4. Send the request and observe the HTTP 302 response.
5. Open the crafted URL in a browser and verify that it redirects to Bing.

## POC (Proof of Concept)

**Lab Address:** https://links.pwnedbox.site/

**HTTP Method:** GET

**Payload:**
```text
////bing.com/index.php?category=developers&page=1
```

**Crafted URL:**
```text
https://links.pwnedbox.site////bing.com/index.php?category=developers&page=1
```

**Request:**
```http
GET ////bing.com/index.php?category=developers&page=1 HTTP/2
Host: links.pwnedbox.site
```

**Observed Result:**

The server responds with `HTTP/2 302 Found` and a `Location` header containing the attacker-controlled path:

```http
HTTP/2 302 Found
Location: ////bing.com/index.php?category=developers&page=1
```

When opened in the browser, the crafted URL redirects the user to the external Bing website.

**Screenshot Evidence:**
<img width="1917" height="968" alt="image" src="https://github.com/user-attachments/assets/322cd779-a768-4f1d-89ac-bc1ce5eaa33f" />

<img width="1919" height="1000" alt="image" src="https://github.com/user-attachments/assets/bf077093-1bc1-4757-9e89-5b15a0870514" />

<img width="1916" height="970" alt="image" src="https://github.com/user-attachments/assets/e6a991f6-c4db-4286-8c45-812236a35c7b" />

## Impact

- It allows attackers to redirect users from a trusted application domain to malicious or phishing websites.
- It may lead to credential theft and sensitive information disclosure through convincing phishing pages.
- It can be abused to distribute malicious links that appear to originate from a trusted domain.
- It may damage the application's reputation and user trust.

## Remediation

1. Validate redirect destinations against a strict allowlist of trusted domains.
2. Reject external destinations and suspicious URL paths containing manipulated slash sequences.
3. Use predefined internal paths instead of accepting arbitrary user-controlled redirect destinations.
4. Normalize and validate URLs before processing redirects, and use a safe default destination when validation fails.







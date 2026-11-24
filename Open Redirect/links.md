## Title
Open Redirect in Omise Developer Blog

## Description
**Affected URL:** `https://links.pwnedbox.site/`

The website accepts a modified URL containing an external destination. During testing, accessing a crafted URL resulted in navigation away from the original Omise Developer Blog to Bing.

## Steps to Reproduce
1. Visit `https://links.pwnedbox.site/index.php?category=interview&page=1`.
2. Modify the URL to `https://links.pwnedbox.site///bing.com`.
3. Press Enter.
4. Observe the browser navigating to Bing.

## POC (Proof of Concept)
**Payload:**
`https://links.pwnedbox.site///bing.com`

The screenshots demonstrate the original website, modified URL, and resulting external navigation.

<img width="1917" height="968" alt="image" src="https://github.com/user-attachments/assets/322cd779-a768-4f1d-89ac-bc1ce5eaa33f" />

<img width="1919" height="1000" alt="image" src="https://github.com/user-attachments/assets/bf077093-1bc1-4757-9e89-5b15a0870514" />

<img width="1916" height="970" alt="image" src="https://github.com/user-attachments/assets/e6a991f6-c4db-4286-8c45-812236a35c7b" />




## Impact
- Attackers may abuse the trusted domain to distribute deceptive links.
- Users could be redirected to phishing websites.
- This could facilitate credential theft and social engineering attacks.

## Remediation
- Validate redirect destinations against a strict allowlist.
- Reject unauthorized external URLs.
- Normalize URLs before validation.
- Block protocol-relative and malformed redirect destinations.
- Use predefined server-side redirect destinations.
- Redirect invalid requests to a safe internal page.
- Log suspicious redirect attempts.

## Title
Open Redirect via URL Parameter

## Description
**Affected URL:** https://redirect.pwnedbox.site/

The `url` parameter is vulnerable to an Open Redirect vulnerability, allowing attackers to redirect users from the trusted website to arbitrary external websites by manipulating the destination URL. This behavior can be abused by crafting links that appear to originate from a legitimate domain while silently directing users to attacker-controlled websites. No authentication is required to trigger the demonstrated redirect.

## Steps to Reproduce
1. Open https://redirect.pwnedbox.site/.
2. Modify the `url` parameter with an external destination:
   ```text
   https://redirect.pwnedbox.site/?url=https://bing.com%2F
   ```
3. Visit the crafted URL.
4. Observe that the browser redirects to the external website (Bing).
5. Confirm the redirect behavior using Burp Suite Repeater or Intruder with different destination URLs.

## POC (Proof of Concept)
**Affected Domain:** https://redirect.pwnedbox.site/

**Payload:**
```text
https://redirect.pwnedbox.site/?url=https://bing.com%2F
```

**Observed Result:** Successfully redirected from `redirect.pwnedbox.site` to `bing.com`. The browser address bar confirms that navigation leaves the original domain and reaches the supplied external destination.

<img width="1919" height="972" alt="image" src="https://github.com/user-attachments/assets/3132ca98-9611-4e2b-a8f4-9821dd746cc6" />

<img width="1919" height="977" alt="image" src="https://github.com/user-attachments/assets/416d9d21-f2e4-4c73-ae8b-af7c393a9fc4" />

<img width="1919" height="962" alt="image" src="https://github.com/user-attachments/assets/325e9920-97ef-433e-b5d6-98f2ccf6a26a" />




## Impact
- Attackers can redirect users to malicious or phishing websites.
- It can be exploited to make phishing links appear trustworthy.
- It can facilitate credential theft and other social engineering attacks.

## Remediation
- Allow redirects only to trusted, predefined domains.
- Validate the destination URL against a strict allowlist.
- Reject URLs pointing to unauthorized external domains.
- Restrict accepted URL schemes to prevent dangerous protocols.
- Normalize and decode URLs before validation to prevent encoding-based bypasses.
- Prefer relative paths instead of accepting arbitrary redirect URLs.

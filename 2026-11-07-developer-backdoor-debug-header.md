# Developer Backdoor via Debug Header on picoCTF "Crack the Gate 1"

**Source:** picoCTF — Web Exploitation

## Summary
An HTML comment in the page source disclosed a debug bypass: a ROT13-encoded
note instructing developers to use the header `X-Dev-Access: yes` to skip
authentication. Adding this header to the login request authenticates the
attacker as `ctf-player@cylabacademy.org` without a password.

## Vulnerability
- CWE-798: Use of Hard-coded Credentials (or: CWE-489: Active Debug Code)
- CWE-200: Exposure of Sensitive Information

The page source contained a commented-out developer note (ROT13-encoded)
that disclosed a header-based authentication bypass, left in production.

## Steps to Reproduce
1. Open the login page and view source (DevTools → Elements → body).
2. Locate the HTML comment containing the ROT13 string:
   `Wnpx - grzcbenel olcnff: hfr urnqre "K-Qri-Npprff: lrf"`
3. ROT13-decode it: `Jack - temporary bypass: use header "X-Dev-Access: yes"`
4. Log in with any password for `ctf-player@cylabacademy.org`.
5. In DevTools → Network, right-click the login request → Copy as cURL.
6. Append `-H 'X-Dev-Access: yes'` to the curl command and run it.
7. The response returns the flag.

## Impact
Authentication bypass: an attacker who reads the page source can
authenticate as any user (or at least `ctf-player`) without credentials.
The "Remove before pushing to production!" comment confirms the bypass
was known to be unsafe and was left in anyway.

## Remediation
- Remove all debug/dev bypass code and comments before deployment.
- Never ship developer notes in HTML source; use server-side feature flags.
- Enforce authentication at the server, not via a header the client can set.
- Add a pre-deploy check for commented-out code and debug headers.

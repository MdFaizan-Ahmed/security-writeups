# Insufficient Session Expiration on picoCTF "Old Sessions"

**Source:** picoCTF 2026: Web Exploitation 

## Summary
The application sets session cookies without an expiration and exposes
all active sessions (including the admin's) via a `/sessions` endpoint.
This allows an attacker to hijack the admin session by substituting the
exposed session ID into their own cookie.

## Vulnerability
- CWE-1463: Insufficient Session Timeout Value (no expiration on the session cookie)
- CWE-200: Exposure of Sensitive Information (the `/sessions` endpoint lists all active session IDs)

The `session` cookie has no expiration, and the `/sessions` endpoint
lists every active session ID alongside its username.

## Steps to Reproduce
1. Register and log in as a normal user.
2. Navigate to `/sessions`; the response exposes all active session IDs,
   including one with `'key': 'admin'`.
3. Copy the admin session ID.
4. Open DevTools → Application → Cookies, and replace your `session`
   cookie value with the admin's ID.
5. Refresh the homepage; you are now authenticated as admin, and the
   flag is displayed.

## Impact
Session hijacking and privilege escalation: any authenticated user can
become admin without credentials.

## Remediation
- Never expose session IDs via any endpoint.
- Set `HttpOnly`, `Secure`, `SameSite` on session cookies.
- Enforce absolute expiration and idle timeouts.
- Invalidate sessions server-side on logout.

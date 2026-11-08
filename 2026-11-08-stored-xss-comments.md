# Stored Cross-Site Scripting (XSS) in Comment Field on PortSwigger "Stored XSS into HTML context with nothing encoded"

**Source:** PortSwigger Web Security Academy — Cross-Site Scripting (Apprentice)

## Summary
The comment functionality saves user input to the database and renders it without sanitization. An attacker can inject a persistent script that executes in the browser of every user who views the page.

## Vulnerability
CWE-79: Improper Neutralization of Input During Web Page Generation ('Cross-site Scripting').

The application stores the comment body in the backend database and later retrieves and renders it directly into the HTML without encoding.

## Steps to Reproduce
1. Navigate to a blog post and scroll to the comment section.
2. Submit a comment containing the payload: `<script>alert(1)</script>`.
3. Reload the page.
4. The server serves the stored payload as raw HTML, and the browser executes the JavaScript for anyone who views the post.

## Impact
Persistent session hijacking, credential theft, or wormable attacks (a script that automatically spreads by submitting itself as a comment). Because it is stored, it does not require a crafted link; merely viewing the page triggers the payload.

## Remediation
- Context-aware output encoding: HTML-encode all stored user input before rendering.
- Sanitize input on the server side (e.g., using an allowlist of safe HTML tags).
- Implement a strict Content Security Policy (CSP).

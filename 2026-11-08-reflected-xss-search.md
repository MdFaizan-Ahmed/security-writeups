# Reflected Cross-Site Scripting (XSS) in Search Parameter on PortSwigger "Reflected XSS into HTML context with nothing encoded"

**Source:** PortSwigger Web Security Academy: Cross-Site Scripting (Apprentice)

## Summary
The search functionality reflects user input directly into the HTML response without sanitization. An attacker can inject a malicious script via the URL, which executes in the victim's browser when they visit the crafted link.

## Vulnerability
CWE-79: Improper Neutralization of Input During Web Page Generation ('Cross-site Scripting').

The application takes the `search` parameter from the URL and inserts it directly into an `<h1>` tag in the HTML response without encoding angle brackets.

## Steps to Reproduce
1. Navigate to the search page and enter a test string (e.g., `test`).
2. Observe the URL: `/?search=test`.
3. Inspect the HTML response. The input is reflected as: `<h1>0 search results for 'test'</h1>`.
4. Inject a script by breaking out of the `<h1>` tag:
   `/?search=</h1><script>alert(1)</script>`
5. The browser parses the injected tags and executes the JavaScript.

## Impact
Session hijacking, credential theft, or performing actions on behalf of the user. Because it is reflected, the attacker must deliver the payload via a crafted link (e.g., phishing) and convince the victim to click it.

## Remediation
- Context-aware output encoding: HTML-encode angle brackets, quotes, and ampersands before rendering user input.
- Implement a strict Content Security Policy (CSP) to block inline scripts.

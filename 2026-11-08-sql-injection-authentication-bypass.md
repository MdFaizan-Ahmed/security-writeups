# Authentication Bypass via SQL Injection on PortSwigger "SQL Injection Vulnerability Allowing Login Bypass"

**Source:** PortSwigger Web Security Academy — SQL Injection (Apprentice)

## Summary
The login form concatenates the username directly into the backend SQL query. Injecting `administrator'--` comments out the password verification step, allowing authentication as the administrator without valid credentials.

## Vulnerability
CWE-89: SQL Injection.

The backend query is constructed as:
`SELECT * FROM users WHERE username = '<input>' AND password = '<input>'`

Because the input is concatenated, the `--` comment sequence truncates the query, removing the `AND password = '...'` condition entirely.

## Steps to Reproduce
1. Navigate to the login page.
2. Enter `administrator'--` in the Username field.
3. Enter any arbitrary string in the Password field.
4. Submit the form.
5. The resulting query becomes:
   `SELECT * FROM users WHERE username = 'administrator'--' AND password = 'anything'`
6. The `--` comments out the password check. The database matches the `administrator` user and returns a valid session.

## Impact
Full account takeover: an attacker can log in as any known user (including administrators) without knowing their password. 

## Remediation
- Use parameterized queries (prepared statements) for all database interactions.
- Never concatenate user input directly into SQL strings.

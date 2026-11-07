# SQL Injection in Product Category Filter on PortSwigger "SQL Injection Vulnerability in WHERE Clause"

**Source:** PortSwigger Web Security Academy: SQL Injection (Apprentice)

## Summary
The product category filter concatenates user input directly into a SQL
query without sanitization. Injecting `' OR 1=1--` bypasses the
`released = 1` filter, exposing all products (including unreleased ones).

## Vulnerability
CWE-89: SQL Injection.

The backend query is constructed as:
`SELECT * FROM products WHERE category = '<input>' AND released = 1`

User input is concatenated into the string, so the `--` comment sequence
removes the `released = 1` condition entirely.

## Steps to Reproduce
1. Open the product category filter (e.g., `category=Gifts`).
2. Replace the value with `' OR 1=1--`.
3. The resulting query becomes:
   `SELECT * FROM products WHERE category = '' OR 1=1--' AND released = 1`
4. `OR 1=1` matches every row; `--` comments out the `AND released = 1`
   filter.
5. The page returns all products, including unreleased ones.

## Impact
Data exposure: an attacker can bypass filters and retrieve records they
shouldn't see. With more advanced payloads (`UNION SELECT`), the same
injection point can extract data from other tables (usernames, passwords,
etc.).

## Remediation
- Use parameterised queries (prepared statements) for all SQL.
- Never concatenate user input into SQL strings.
- Apply least-privilege database permissions (the app shouldn't have
  access to tables it doesn't need).

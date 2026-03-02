# A01  Broken Access Control

**OWASP Reference:** A01:2021  
**Environment:** DVWA Low security  
**Tool:** Firefox, URL manipulation

---

## What is it?

Broken Access Control happens when users can act outside their intended permissions  accessing other users' data, admin pages, or performing actions they should not be able to.

---

## Lab: Direct URL Access (DVWA)

### Steps
1. Log in to DVWA as a regular user (not admin)
2. Note the URL after login:
   ```
   http://192.168.56.101/dvwa/vulnerabilities/fi/?page=include.php
   ```
3. Manually navigate to the admin panel:
   ```
   http://192.168.56.101/dvwa/security.php
   ```
4. DVWA allowed access  no authorisation check was performed on the route

### What happened?
The application only checked if the user was *logged in*, not whether the logged-in user had *permission* to access that page. Changing the URL directly bypasses the UI-level access restrictions.

---

## Lab: IDOR (Insecure Direct Object Reference)

DVWA's user listing exposes IDs in the URL:
```
http://192.168.56.101/dvwa/vulnerabilities/idor/?user_id=1
http://192.168.56.101/dvwa/vulnerabilities/idor/?user_id=2
```

Incrementing `user_id` retrieves other users' profile data with no ownership check.

---

## Remediation

- Implement server-side authorisation checks on every route  never rely on hiding URLs in the UI
- Use indirect references (e.g. map `ref=abc123` to actual DB IDs server-side) instead of exposing raw IDs
- Apply principle of least privilege: each role should only access what it needs

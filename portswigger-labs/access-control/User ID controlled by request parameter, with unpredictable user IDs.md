# User ID controlled by request parameter, with unpredictable user IDs

**Platform:** PortSwigger Web Security Academy

**Difficulty:** Apprentice

**Category:** Access Control

**Severity:** High

**Date:** June 2026

---

## 1. Summary
The application exposes user account information through a request parameter that contains a user identifier. Because the server does not verify whether the authenticated user is authorized to access the requested account, an attacker can modify the identifier to access another user's sensitive data, such as their API key.

---

## 2. Steps to Reproduce
1. Log in using the credentials wiener:peter.
2. Navigate to the "My Account" page.
3. Observe the request containing the user identifier parameter:
GET /my-account?id=<user-id>   
4. Send this request to Burp Repeater using Burp Suite.
5. Browse the application and locate a blog post created by another user (for example: carlos).
6. Identify the username of the target user from the blog post.
7. Modify the id parameter in the Repeater request to the target username:
GET /my-account?id=carlos
8. Send the request.
9. Observe that the response now displays the API key belonging to the other user.

---

## 5. Proof of Concept
`/my-account?id=`   

``id=carlos``

---

## 03.Root Cause

The application relies on a user-controlled identifier to determine which account data should be returned. The server does not verify whether the authenticated user is authorized to access the requested account.

---

## 4. Impact
An attacker can modify the user identifier parameter to access sensitive information belonging to other users, such as API keys. This may allow attackers to impersonate other users or abuse exposed credentials.

---

## 5. Suggested Fix
The application should implement server-side authorization checks to ensure that users can only access their own account data. Sensitive identifiers should not be directly exposed or trusted from user-controlled parameters.

---

## 6. References
- CWE-639: Authorization Bypass Through User-Controlled Key	https://cwe.mitre.org/data/definitions/639.html
- OWASP: Testing for Insecure Direct Object References	https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/04-Testing_for_Insecure_Direct_Object_References
- PortSwigger: IDOR Vulnerabilities	https://portswigger.net/web-security/access-control/idor


---

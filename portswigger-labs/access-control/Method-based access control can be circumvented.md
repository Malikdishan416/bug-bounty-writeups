# Method-Based Access Control Can Be Circumvented

**Platform:** PortSwigger Web Security Academy
**Category:** Access Control
**Difficulty:** Practitioner
**Date:** June 2026

---

## Summary
The application enforces access control on the `POST /admin-roles` endpoint but fails to apply the same checks when the request method is changed to `GET`. A low-privileged user can switch the HTTP method to bypass the restriction entirely and promote themselves to administrator without authorization.

## Steps to Reproduce
1. Log in as `administrator:admin` and navigate to the Admin Panel
2. Select any user and click Upgrade
3. In Burp Suite → Proxy → HTTP History, locate the `POST /admin-roles` request
4. Send it to Repeater
5. Log out and log back in as `wiener:peter`
6. In HTTP History, locate the `GET /my-account?id=wiener` request
7. Copy the session cookie value
8. In Repeater, replace the session cookie with wiener's session cookie
9. Right-click the request and select "Change request method"
10. Change the `username` parameter to `wiener`
11. Send the request — wiener is now promoted to administrator

## Impact
A low-privileged attacker can escalate their account to administrator by switching the HTTP method, bypassing access controls that only check one method type. This grants full control over user management and any admin-gated functionality in the application.

## Fix
Enforce access control checks on every HTTP method independently — never assume that restricting `POST` also restricts `GET` on the same endpoint. Use an allowlist of permitted methods per endpoint and reject all others with `405 Method Not Allowed`. Validate user roles server-side on every request regardless of the method used.

## Lesson
HTTP method is not a security boundary. If an endpoint enforces access control on `POST` but not `GET`, switching the method is all it takes to bypass it. Always apply authorization checks at the action level, not the method level.

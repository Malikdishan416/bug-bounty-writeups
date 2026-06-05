# Referer-Based Access Control

**Platform:** PortSwigger Web Security Academy
**Category:** Access Control
**Difficulty:** Practitioner
**Date:** June 2026

---

## Summary
The application uses the Referer header to determine whether a request to a privileged endpoint originated from the admin panel. Because the Referer header is fully attacker-controlled, a low-privileged user can forge it and promote themselves to administrator without any valid authorization.

## Steps to Reproduce
1. Log in as `administrator:admin` and navigate to the Admin Panel
2. Select any user and click Upgrade — confirm the action
3. In Burp Suite → Proxy → HTTP History, locate the `POST /admin-roles` request
4. Send it to Repeater
5. Log out and log back in as `wiener:peter`
6. Open DevTools → Application → Cookies and copy the session cookie value
7. In Repeater, replace the session cookie with wiener's session cookie
8. Change the `username` parameter in the request body to `wiener`
9. Send the request — wiener is now promoted to administrator

## Impact
A low-privileged attacker can escalate their own account to administrator by forging the Referer header, bypassing access control entirely. This grants full control over user management and any admin-gated functionality in the application.

## Fix
Never use the Referer header as an access control mechanism — it is fully attacker-controlled and can be set to any arbitrary value. Enforce authorization server-side by validating the authenticated user's role from their session on every sensitive request.

## Lesson
If the server decides whether to allow a privileged action based on where a request claims to have come from rather than who sent it, the access control is broken. Always verify identity and role — never origin.

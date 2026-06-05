# URL-Based Access Control Can Be Circumvented

**Platform:** PortSwigger Web Security Academy
**Category:** Access Control
**Difficulty:** Practitioner
**Date:** June 2026

---

## Summary
The application blocks direct access to `/admin` at the front-end level but processes the `X-Original-URL` header on the backend without any authorization check. An unauthenticated attacker can inject this header to route their request to any restricted admin endpoint, bypassing access controls entirely.

## Steps to Reproduce
1. Navigate to the Admin Panel from the top-right — observe access is denied
2. In Burp Suite → Proxy → HTTP History, locate the `GET /admin` request
3. Send it to Repeater
4. In the request line, replace `/admin` with `/`
5. Add the following header to the request: `X-Original-URL: /admin`
6. Send the request — observe the admin panel is now accessible
7. To delete a user, change the header to `X-Original-URL: /admin/delete`
8. Add `?username=carlos` to the request line
9. Send the request — the user is deleted

## Impact
An unauthenticated attacker can bypass front-end access controls on any restricted endpoint by injecting the `X-Original-URL` header. This allows full administrative compromise — including deleting users, modifying accounts, and accessing any admin-only functionality — without valid credentials.

## Fix
Never use `X-Original-URL`, `X-Rewrite-URL`, or any client-controlled header to make access control decisions server-side. Configure your reverse proxy to strip these headers from all incoming client requests before they reach the backend. Enforce role and session-based authorization at the application layer on every request regardless of what headers are present.

## Lesson
Front-end URL blocking is not access control. If the backend blindly trusts headers like `X-Original-URL` to determine what page to serve, the entire access control layer can be bypassed with a single header injection. Authorization must be enforced server-side at the action level, not at the URL routing level.

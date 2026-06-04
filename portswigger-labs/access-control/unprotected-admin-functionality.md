# Unprotected Admin Functionality

**Platform:** PortSwigger Web Security Academy
**Category:** Access Control
**Difficulty:** Apprentice
**Date:** June 2026

---

## Summary
The application exposes an admin panel at a predictable URL without enforcing any authentication or authorization checks. Any user who navigates directly to the endpoint gains full admin access.

## Steps to Reproduce
1. Open the target application
2. View the page source (right click → View Page Source)
3. Find the /administrator-panel path referenced in the source
4. Navigate directly to /administrator-panel in the URL bar
5. Full admin panel is accessible with no login or verification required
6. Delete target user to solve the lab

## Impact
An unauthenticated attacker can navigate to the admin panel without authentication or authorization and perform actions such as user deletion, user role alteration or retrieving sensitive data that only admins are allowed to see.

## Fix
The application must require server-side authentication and authorization checks on every request to sensitive endpoints, regardless of whether the URL is publicly known.

## Lesson
Never rely on URL secrecy to protect sensitive functionality. Access control must be enforced server-side on every single request.

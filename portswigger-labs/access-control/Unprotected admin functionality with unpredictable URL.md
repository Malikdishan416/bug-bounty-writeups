# Unprotected admin functionality with unpredictable URL

**Platform:** PortSwigger Web Security Academy
**Category:** Access Control
**Difficulty:** Apprentice 
**Date:** June 2026

---

## Summary
An attacker can perform privileged actions by accessing to the admin panel without authorization checks by locating hidden administrative endpoints.

## Steps to Reproduce

1. In the URL add `/robots.txt` 
2. Observe that it does not lead to anything.
3. View the page source of the application to see if there is any JS that could lead to the admin panel.
4. Identify the admin directory in that JS 
5. paste it in the URL
6. Use the delete functionality to remove a user.

## Impact
An unauthenticated attacker can locate hidden the admin directory in page source of the application without authentication or authorization and perform privileged actions such as user deletion, retrieving sensitive data or even change user roles. this leads to unauthorized administrative control over the application.

## Fix
Implement proper authentication and role-based authorization checks for administrative endpoints. Sensitive functionality should not rely solely on hidden URLs and should enforce server-side access control. Administrative endpoint paths must not be exposed in client-side JavaScript source code

## Lesson
Always view the page source to check wheather there are hidden directories or vulnerable endpoints exist.

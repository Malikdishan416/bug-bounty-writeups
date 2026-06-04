# User role controlled by request parameter

**Platform:** PortSwigger Web Security Academy
**Category:** Access Control
**Difficulty:** Apprentice
**Date:** June 2026

---

## Summary
The application accepts a user-supplied role parameter in the login request and uses it to assign privileges without verifying it server-side.

## Steps to Reproduce
1. Log in using the credentials wiener:peter.
2. Intercept the login request using Burp Suite.
3. Observe that the request contains a cookie or parameter named admin with the value false.
4. Navigate to the "My Account" page  
5. Send the request to Burp Repeater.
6. Modify the admin parameter from false to true.
7. Send the modified request.
8. Observe that the response now contains the admin panel.
9. Access the admin panel 
10. Delete a user.

## Impact
An authenticated attacker gains access to admin panel to escalate their privileges and perform admin actions such as user deletion or modifying user roles by intercepting the login request and modifying the value of user role parameter from false to true.

## Fix
The application must confirm the session cookie from the server matches the one in the database before continuing. The server must never accept role or privilege values from the request — the role must only come from the database.

## Lesson
Always intercept login requests and check for role or privilege parameters — if the application trusts client-supplied values for access control, it's vulnerable.

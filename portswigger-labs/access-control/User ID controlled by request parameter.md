#  User ID controlled by request parameter

**Platform:** PortSwigger Web Security Academy
**Category:** Access Control 
**Difficulty:** Apprentice 
**Date:** June 2026

---

## Summary
Access control vulnerability in the user account page allows an attacker to retrieve a victim user’s API key

## Steps to Reproduce
1. Navigate to ‘my account’ from above.
2. Log in with the given credentials (wiener:peter)
3. Go to burp>proxy>https history 
4. locate the `GET /my-account?id=wiener HTTP/2` request 
5. send it to the repeater (or press CTRL+R)
6. Replace the username with the victim’s (carlos)
7. Click send
8. In the response section search the word ‘carlos’
9. Identify the api key
10. Copy that and submit as the solution.

## Impact
An authenticated attacker can gain authorized access to a user's account by modifying the value of id parameter of `/my-account`request and retrieve the api key. This could lead to complete user account compromise.

## Fix
The application must not consider or determine which account to display solely based on the authenticated session and account identity must be derived server-side from the verified session token with strict checks ensuring the authenticated user matches the requested resource owner.

## Lesson
Always look for an id parameter in a request to try to change it.

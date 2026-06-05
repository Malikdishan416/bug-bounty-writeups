# User ID controlled by request parameter with data leakage in redirect

**Platform:** PortSwigger Web Security Academy
**Category:** Access Control 
**Difficulty:** Apprentice 
**Date:** June 2026

---

## Summary
Access control vulnerability allows an attacker to log in as a user by retriving the api key without the knowledge of the user/victim

## Steps to Reproduce
1. Go to my account
2. log in as wiener and peter
3. in burp locate the `GET /my-account?id=wienerHTTP/2` request
4. send it to repeater
5. change wiener to carlos (victim’s) username
6. click send
7. in response search “api key”
8. identify it,copy and submit solution

## Impact
An authenticated attacker  can intercept the redirect response in Burp Suite and read the victim's API key from the response body before it is discarded by the browser. This data leakage in redirect responses allows complete credential theft without the victim's awareness and without bypassing any authentication mechanism directly.

## Fix
The server must not include any sensitive user data in redirect responses. When issuing a 302 redirect the response body should be empty. Sensitive data must only be returned after full authorization verification is complete and the response is confirmed as a final page load, not a redirect. Implement server-side checks that verify session ownership before any account data is processed, not just before it is displayed.

## Lesson

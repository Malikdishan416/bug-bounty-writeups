# Username enumeration via different responses 

**Platform:** PortSwigger Web Security Academy
**Category:** Authentication
**Difficulty:** practitioner
**Date:** June 2026

---

## Summary
The login endpoint returns visibly different responses (different length + different error message) depending on whether the username exists or not. This allows an attacker to enumerate valid usernames, making subsequent password brute-force attacks far more effective.

## Steps to Reproduce
1. Intercept any login request (invalid username + password) 
2. Send it to Intruder.
3. Mark the **username** parameter as the payload position (§username§).
4. Load the provided username wordlist in Payloads → Simple list.
5. Start Attack and sort by response length.
6. Identify the username with different length/response (valid username).
7. Replace username with the valid one, mark **password** parameter, load password wordlist and attack again.
8. Login with the discovered credentials.

## Impact

## Fix

## Lesson

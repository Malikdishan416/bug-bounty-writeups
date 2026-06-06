# Password brute-force via password change

**Platform:** PortSwigger Web Security Academy
**Difficulty:** Practitioner
**Category:** Authentication 
**Severity:** Medium
**Date:** June 2026

---

## 1. Summary
The password change endpoint allows any authenticated attacker to submit password-change requests targetting other accounts by modifying the username parameter value with the authentication oracle reveals weather the current password is incorrect. This allows brute-force attacks against arbitrary users.
---

## 2. Methodology
**My Observations** 

- When the current password is correct but the new passwords mismatch = “New passwords do not match”
- When the current password is incorrect and the new passwords match = Goes back to login page*
This is a **classic authentication oracle**.

- When the current password is incorrect and the new passwords mismatch = Goes back to login page* and sometimes “Current password is incorrect”

> So weather the new passwords match or don't if current one is incorrect, goes back to login page
> 

- When attempted too many incorrect passwords and usernames = “You have made too many incorrect login attempts. Please try again in 1 minute(s).”
That's a **login page** rate limit.

#### This means: **The server checks the current password FIRST, and only if it's correct does it then check if the new passwords match.**

My assumption (Hypothesis) 👇

<aside>

How about i restart this in this way, I go as wiener and peter and change the password with current incorrect but with matching new passwords, take that request send to intruder so i can replace wiener with carlos and only add payload to value of current cause thats the only thing incorrected, and once i find a match it would mean that i changed password of carlos?

</aside>

> 
> 
> 
> **First understand normal behavior.
> Then automate.**
> 

<aside>

My idea was:

Known endpoint: change password
↓
Change username to carlos
↓
Bruteforce current-password
↓
Keep new passwords matching
↓
Look for different behavior

</aside>

Where I was wrong is I forgot to consider the fact (below)

#### Fact: The password change form accepts a `username` parameter and validates the `current-password` against it **without properly checking if the session belongs to that user**.

What I assumed
*"Is session the only user identification?" but* No — but it's the *strongest* one.

| What the server sees | Trust level |
| --- | --- |
| Session cookie | **High** — this is who you ARE |
| Username in body/URL | **Low** — this is who you CLAIM to be |

What I learned : 
 I assumed the server would bind the username parameter to the
  session. It didn't. Session and body parameters are validated
  independently — when they disagree, that's a vulnerability.
- I initially used matching new passwords, which triggered account
  lockout. Mismatched new passwords bypass lockout while still
  exposing the oracle.
- "First understand normal behavior. Then automate." — I spent
  2.5 hours brute-forcing because I missed the lockout behavior.
  Manual observation first would have saved hours.

## 3. Vulnerability Details
Authentication Oracle in Password Change Endpoint Allows Brute-Force of Arbitrary User Credentials

---

## 4. Steps to Reproduce
1. Log in as any valid user (e.g., `wiener:peter`)
2. Navigate to the password change page (It automatically loads)
3. Enter any placeholder value in the current password page
4. Enter any **mismatched** values in the new password fields (e.g., `newpass1` and `newpass2`)
5. Intercept the `POST /my-account/change-password` request in Burp Proxy
6. Send the request to Intruder
7. Clear all payload positions
8. Add a payload position to the `current-password` parameter value only
9. Replace the `username` value with the target victim's username (`carlos`)
10. Ensure `new-password-1` and `new-password-2` remain **different** values
11. Load the candidate password list into the payload
12. In Intruder Settings → Grep - Match, add: `New passwords do not match`
13. Start the attack
14. Identify the request where the Grep Match column shows a match — this is the correct password
15. Log in as the victim using the discovered password

---

## 5. Proof of Concept
```
The following screenshot demonstrates successful brute-force of
Carlos's password using the authentication oracle:
```

!image.png

!image.png

!image.png

---

## 6. Impact
An authenticated attacker can gain access to the victim’s account via password-change endpoint without the server validating the account by matching the session with the value request parameters before sending requests. By gaining access to the victims account attacker can seize complete control over the account and perform actions such as accessing personal data( DOB, full name ) , order history, saved payment methods.

---

## 7. Suggested Fix
Remove the username parameter from the password change request because the server already knows the user from the session cookie. Do not let an attacker apply a different username. If the username parameter must remain, make sure to validate that it matches with the session identity before continuing to password change. Use a single generic error message for all password change failures :"Password change failed. Please check your details and try again." Do not reveal weather or not the current password is correct .

---

## 8. References

- CWE-204: Observable Response Discrepancy — https://cwe.mitre.org/data/definitions/204.html
- CWE-639: Authorization Bypass Through User-Controlled Key — https://cwe.mitre.org/data/definitions/639.html
- OWASP: Testing for Weak Lock Out Mechanism — https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/03-Testing_for_Weak_Lock_Out_Mechanism
---

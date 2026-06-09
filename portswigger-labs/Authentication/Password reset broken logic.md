#  Password reset broken logic

**Platform:** PortSwigger Web Security Academy

**Difficulty:** Apprentice 

**Category:**  Authentication 

**Severity:** High

**Date:** June 2026

---

## 1. Summary
Password Reset Endpoint Trusts User-Supplied Username, Enabling Arbitrary Account Takeover
---

## 3. Vulnerability Details
Password reset endpoint accepts the username parameter and its value that determines which account to send to password reset email

---

## 4. Steps to Reproduce
1. In the website navigate to the top and click ‘My Account’.
2. Right below the password box, click  ‘Forgot password’
3. Enter the username (wiener) and click submit
4. Go and click the email client on top
5. Click the link and reset the password.
6. In burp>proxy>https history locate the `POST /forgot-password?temp-forgot-password...`request.
7. Click and send it to repeater.
8. replace the username with the target (carlos).
9. Delete the value of ‘temp-forgot-password-token’ in URL and request body.

*The application validates the username parameter but does not require a valid token when the token field is empty. This allows the attacker to specify any target account.*

10. Click send to send the request.
11. Log in as carlos to solve the lab.

---

## 6. Impact
An unauthenticated attacker could modify the username parameter by intercepting the forgot password request and log in to victims account without any authentication or authorization checks, this leads to complete account takeover.
---

## 7. Suggested Fix
The server must validate that the password reset token is present, unexpired before processing any password chang. The token must be invalidated immediately after use. The username in the request body must not be trusted — the account to reset should be determined solely from the validated token, not from user-supplied input.
---

## 8. References
- CWE-640: Weak Password Recovery Mechanism for Forgotten Password	https://cwe.mitre.org/data/definitions/640.html
- OWASP: Testing for Weak Lock Out Mechanism	https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/03-Testing_for_Weak_Lock_Out_Mechanism
- PortSwigger: Authentication Vulnerabilities	https://portswigger.net/web-security/authentication

---

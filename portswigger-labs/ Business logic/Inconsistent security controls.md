#Inconsistent security controls 

**Platform:** PortSwigger Web Security Academy

**Difficulty:** Apprentice

**Category:** Business Logic 

**Severity:**  Medium

**Date:** June 2026

---

## 1. Summary
The change-email endpoint in the application bypasses employee verification and allows arbitrary users to gain admin access
---

## 2. Methodology
# My assumptions

> **Business logic flaw** — the security control exists but is applied inconsistently.
> 

In the solution. There is a "Target" > "Site map" tab in Burp which works for pro versions only. I use free version so I need to find alternatives myself.

1. I tried IDOR to access admin control (by `/admin`)

then the application said “Admin interface only available if logged in as a DontWannaCry user.”

1. In register page it shows a clue
- “If you work for DontWannaCry, please use your @dontwannacry.com email address”
- The lab description also said  “arbitrary users to access administrative functionality that should only be available to company employees.”

The clue is telling exactly what the vulnerability is.

- In order to become a ‘dontwannacry’ user I need to register with my email domain “@dontwannacry.com” so the app treats me as an employee.
1. But then I found out the registration page blocks @dontwannacry.com 
- **B**ecause registered employees obviously  don't re-register.
1. Then the bug was discovered 
- After registering we need to log in. After logged in there is a section to update the email
- It doesn't block “@dontwannacry.com” domain
- Which means it lets me change the email domain to become an employee without **verifying I actually have employee privileges.**
    
    The vulnerability is in change-email endpoint!! It doesn't block arbitrary users from updating the email to their company's without verification.

---

## 3. Vulnerability Details
The application uses email domain (@dontwannacry.com) as the sole mechanism for determining admin access privileges. This check is enforced during account registration — where users are instructed to use their company email — but is completely absent from the email change feature.
The password change endpoint accepts a username parameter and validates the current-password against it without properly checking if the session belongs to that user. Similarly, the email change endpoint accepts any email domain without verification, authorization checks, or confirmation workflow.
Root cause: Inconsistent application of security controls across related features. The registration flow implements domain-based access control, but the email update flow trusts user-supplied input without validating that the user legitimately belongs to the claimed domain.
Result: Any authenticated user can escalate privileges to admin level by simply changing their email address to an arbitrary @dontwannacry.com address, gaining unauthorized access to the admin panel and user deletion functionality.

---

## 4. Steps to Reproduce
1. Enter the lab and go to email client on the top
2. Copy the temporary email ( Something like **`attacker@exploit-0a5b00400346cfff80c0168801de0004.exploit-server.net`)**
3. Go back to lab
4. Navigate to the register page

Notice a message saying “If you work for DontWannaCry, please use your @dontwannacry.com email address” - email domain determines weather users are employees or not.

1. Register with any username, your temporary email, and any password
2. Return to email client
3. Click the link to verify the account
4. Go to my account
5. Enter the registered credentials
6. Hit login

Notice the update email section appeared - observe it is an opportunity to try changing email domain into company's 

1. Put an email, replace the domain with @dontwannacry.com 
2. Click update email
3. In the address bar and type `/admin` — observe admin access is granted
4. Delete carlos and solve the lab

---

## 5. Proof of Concept
`https://[LAB-ID].web-security-academy.net/admin`

`attacker@[YOUR-LAB-DOMAIN].exploit-server.net`

---

## 6. Impact
An authenticated attacker can navigate to the admin panel and perform user deletions by updating the email domain into @dontwannacry.com in change-email endpoint and become an employee of DontWannaCry without any authorization checks or verifications. 

---

## 7. Suggested Fix
 Application must verify the user when the email is changed to @dontwannacry.com by sending a confirmation link to the new address before applying changes. Alternatively implement admin approval process for any email domain changes that grants admin privileges 

---

## 8. References
- CWE-639: Authorization Bypass Through User-Controlled Key	https://cwe.mitre.org/data/definitions/639.html
- OWASP: Testing for Business Logic	https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/
- PortSwigger: Business Logic Vulnerabilities	https://portswigger.net/web-security/logic-flaws


---

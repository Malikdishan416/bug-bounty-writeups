## Multi-step process with no access control on one step

**Platform:** PortSwigger Web Security Academy

**Difficulty:**  Practitioner

**Category:** Access Control 

**Severity:** High

**Date:** June 2026

---

## 1. Summary
Access control vulnerability in admin panel allows an attacker to promote himself to become an administrator 

---

## 2. Methodology

[How you found it. Your observations, hypothesis, failures, final insight. Narrative form.]

---

## 3. Vulnerability Details

[Technical explanation. What is the root cause? What is the oracle? How does the bypass work?]

---

## 4. Steps to Reproduce

1. Navigate to the ‘my account’ on top-right
2. Log in with `administrator:admin` credentials 
3. Access the admin panel
4. Pick a user then click upgrade or downgrade
5. When ask for confirmation click ‘yes’
6. Go to burp>proxy>https history
7. Locate the most recent `POST /admin-roles HTTP/2` request
8. Send to repeater
9. Back to site, log out
10. Log in as `wiener:peter`
11. Go to dev tools (click F12)
12. Go to applications, cookies
13. Copy session cookies
14. Go back to repeater
15. Replace value session cookie with the copied one
16. In bottom of the request , replace the username with `wiener`
17. Click send

---

## 5. Proof of Concept
Cookie: session=rrJIrN3cTZJUIoZuRvgRdsp9b1Hujpx1

---

## 6. Impact


---

## 7. Suggested Fix


---

## 8. References
- CWE-639: Authorization Bypass Through User-Controlled Key	https://cwe.mitre.org/data/definitions/639.html
- OWASP: Testing for Bypassing Authorization Schema	https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/02-Testing_for_Bypassing_Authorization_Schema
- PortSwigger: Access Control Vulnerabilities	https://portswigger.net/web-security/access-control

---

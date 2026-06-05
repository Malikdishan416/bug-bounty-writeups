# Broken brute-force protection, multiple credentials per request

**Platform:** PortSwigger Web Security Academy
**Category:** Authentication
**Difficulty:** Expert
**Date:** June 2026

---

## Summary
Authentication vulnerability in login endpoint accepts JSON 

## Steps to Reproduce
1. Navigate to the my account page.
2. Insert the target username (carlos)
3. Input a arbitrary string to password for testing
4. observe it claims incorrect
5. Go to Burp> Proxy> HTTPS history
6. Locate the `/login` request
7.  Notice the body was JSON format, not form data.
8. Change the password value from a string to an **array** containing the full candidate password list
9. Click send
10. Observe that the Server tested all passwords, found the match, returned a 302 redirect(temporary redirect) meaning login succeeded, server is leading to /my-account
11. Copy the value of session parameter
12. In login page navigate to the web dev-tools (or click f12)
13. Click the cookie section
14. Replace the session id with the copied one
15. Hit enter, logged in as carlos and solve the lab.

- The code to generate password in JSON-based
    
    `raw_passwords = """`
    
    (paste the passwords here)
    
    `""".strip().splitlines()`
    
    `json_output = {
    "username": "carlos",
    "password": raw_passwords
    }`
    
    `import json
    print(json.dumps(json_output, indent=None))`

## Impact
An unauthenticated attacker intercepts the login request and gain authorization access by implementing several possible password values to JSON section of the request. Leading to complete user compromise.

## Fix
The server must not accept JASON by counting each password and the username as one request Instead it must match single username with single password. 

## Lesson
Always examine weather the server in the request of the login endpoint accept JSON 

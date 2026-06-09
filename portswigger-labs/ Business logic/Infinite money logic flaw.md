# Infinite money logic flaw

**Platform:** PortSwigger Web Security Academy

**Difficulty:** Practitioner 

**Category:**  Business Logic 

**Severity:** High (could be critical)

**Date:** June 2026

---

## 1. Summary
A business logic vulnerability exists in the application's purchasing workflow giving the possibility to generate more store credit than the amount spent.
---

## 2. Methodology & Assumptions 
> The store credit was $100 from the start. Gift price was $10.No coupon field was visible on homepage.
> 
- What I assumed was there was a coupon code that gives discounts on gift cards because if coupon > gift card price, then it's a net profit.
- I tried testing common and possible coupon codes such as '`WELCOME', 'PROMO', 'COUPON'` but all of them were invalid. I even tried an XSS on the gift card title but the application sanitized it.
- Tried changing the prices of post requests but the server ignored.

But then I realized the app could be having a **hidden mechanism.**

- I checked Email client - no promo was there. Navigated to the account page to check but no promo.
- - And then I was surfing and found a newsletter section on the bottom. Signed in with a fake email. **Got a coupon code**.

Then I Bought the gift card with the coupon
Also by mistake if at any case the store credit is slighly less than the target item, make sure to restart the attack in intrude.

---

## 3. Vulnerability Details
The business logic flaw vulnerability allows gift cards to be purchased using a discount coupon, granting any authenticated user to spend less money the value received when redeeming the gift card.

---

## 4. Steps to Reproduce
1. Navigate to the ‘my account’ page
2. Login with given credentials (`wiener:peter`)
3. Return to home, Scroll to the bottom and sign up to newsletter
4. Copy the coupon
5. Add the gift card to the cart
6. Go to cart and paste the coupon - observe the total has been deducted
7. Place the order, copy the gift card code
8. Return to my account, paste the code and redeem it
9. Go to burp > proxy > http history
10. Highlight the following requests
11. Go to settings, navigate to sessions section
12. Click add in Session handling rules - Session handling rule editor opens
13. In rule actions section click add and se the drop-down menus to click ‘run on macro’
14. Include the identified requests in the macro sequence
15. Repeatedly execute the workflow to continuously increase the account balance.
16. Use the accumulated balance to purchase the target item and solve the lab.

---

## 5. Proof of Concept
`csrf=u0GrW5BSHdseR6xrWbziTZN9augMM88R&coupon=SIGNUP30`

- Highlighted requests

`POST /cart'

'POST /cart/coupon'

'POST /cart/checkout'

'GET /cart/order-confirmation?order-confirmed=true'

'POST /gift-card`

---

## 6. Impact
An authenticated attacker can generate unlimited funds in the app. Leading them into unauthorized purchases and direct finance loss for the application. In a real-world environment, this exploitation results in significant revenue loss. 

---

## 7. Suggested Fix
Implement server-side validation for all pricing and promotional calculations. Prevent the discount coupons from being applied to gift card purchases and ensure the redeemable value of a gift card would never exceeds the amount actually paid.


---

## 8. References
| CWE-840: Business Logic Errors | `https://cwe.mitre.org/data/definitions/840.html` |
| --- | --- |
| OWASP: Testing for Business Logic Flaws | `https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/10-Business_Logic_Testing/` |
| PortSwigger: Business Logic Vulnerabilities | `https://portswigger.net/web-security/logic-flaws` |

---

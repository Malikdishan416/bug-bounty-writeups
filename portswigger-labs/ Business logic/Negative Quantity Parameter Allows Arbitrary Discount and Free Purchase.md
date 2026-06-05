# Business Logic Flaw: Negative Quantity Parameter Allows Arbitrary Discount and Free Purchase

**Platform:** PortSwigger Web Security Academy
**Category:** Business Logic
**Difficulty:** Apprentice
**Date:** June 2026

---

## Summary
The application's shopping cart does not validate the `quantity` parameter in the add-to-cart request. An authenticated attacker can submit a negative quantity for a low-value item, generating a negative line-item total that acts as a credit against other items in the cart — allowing high-value items to be purchased for $0.

## Steps to Reproduce
1. Log in with credentials `wiener:peter`
2. Add a low-cost item to the cart
3. In Burp Suite, enable Intercept and click "+" in the cart to add another unit
4. Capture the `POST /cart` request and locate the `quantity` parameter
5. Change `quantity=1` to `quantity=-10` and forward the request
6. Observe the cart now shows a line-item total of **-$10.00**
7. Add the high-value Leather Jacket ($1,000.00) to the cart
8. Return to the cart — the -$10.00 credit reduces the jacket's price
9. Remove the low-cost item from the cart
10. Observe the order total is now **$0.00**
11. Click "Place Order" and complete the purchase without valid payment

## Impact
An authenticated attacker can purchase any item in the store for $0.00 or an arbitrarily reduced price by exploiting negative quantity handling. This results in direct revenue loss, inventory theft, potential payment processor penalties, and regulatory exposure under PCI-DSS Requirement 6.5.10.

## Fix
The backend must reject any `quantity` value ≤ 0 with server-side validation — client-side checks are trivially bypassed. Cart totals must be recalculated server-side on every checkout step and must never fall below $0.00. Discounts must be explicitly defined in the database, not derived from negative line items.

## Lesson
Never trust client-submitted data for financial calculations. The `quantity` parameter is user input — user input is always hostile until proven otherwise. Business logic flaws are not exotic attacks; they are features working as designed where nobody asked "what if this value is negative?" This class of bug pays $500–$3,000 on real e-commerce programs because companies directly lose money.

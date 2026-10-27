## Title

Coupon Stacking via Request Replay Allows Applying the Same Discount Multiple Times on E-Commerce Platform.

## Affected URL

https://controllers.pwnedbox.site/subdomains/controllers/cart/apply-coupon

## Description

A business logic vulnerability was identified in the coupon application workflow where the server fails to properly track or invalidate single-use discount coupons per session or user once applied. 

An attacker can intercept the coupon application request and replay it multiple times using a proxy repeater or automated tool. Consequently, the same discount percentage is stacked cumulatively against the order subtotal, allowing attackers to drastically reduce or completely zero out the total order cost.

## Steps to Reproduce

1. Navigate to the target application: `https://controllers.pwnedbox.site/subdomains/controllers/register`[cite: 43].
2. Create a new user account and log into the shop[cite: 43, 44].
3. Add a product to the shopping cart (e.g., Samsung 990 Pro 2TB NVMe)[cite: 44].
4. Enter a valid discount coupon code (e.g., `WELCOME20`) in the coupon input field and click Apply while intercepting the request using a proxy tool (e.g., Burp Suite)[cite: 44].
5. Send the intercepted `apply-coupon` request to the Repeater module[cite: 45].
6. Replay the coupon application request multiple times to stack the discount[cite: 45, 47].
7. Return to the browser and observe that the coupon has been successfully applied multiple times, exponentially increasing the discount amount and reducing the total order cost[cite: 47].
8. Proceed to checkout and complete the payment process at the heavily discounted total[cite: 48].

## POC
<img width="838" height="626" alt="image" src="https://github.com/user-attachments/assets/56228b79-4f3a-474d-a0fc-364b00644a3e" />
<img width="1124" height="1192" alt="image" src="https://github.com/user-attachments/assets/4c0e0035-57ea-45ad-bca5-435efc580bc9" />
<img width="1001" height="1229" alt="image" src="https://github.com/user-attachments/assets/fc012b2d-73cb-4289-95eb-302249f55838" />
<img width="987" height="1040" alt="image" src="https://github.com/user-attachments/assets/683a3a2c-3756-4ebd-a52f-4c9ddfee5a40" />
<img width="1100" height="1103" alt="image" src="https://github.com/user-attachments/assets/7cb7e547-357d-4cfe-9ae3-ae0d9d5e8915" />
<img width="982" height="944" alt="image" src="https://github.com/user-attachments/assets/293e27f7-da90-4374-9201-4bcef5cfe288" />


## Impact

1. Severe financial loss due to unauthorized coupon stacking, enabling customers to acquire goods below cost or for free.
2. Distortion of revenue tracking, discount accounting, and promotional campaign integrity.
3. Bypass of business restrictions intended to limit promotions to a single use per order or user.

## Remediation

1. Implement robust server-side state validation to ensure that a specific coupon code can only be applied once per active cart or user session.
2. Track applied coupons within the user's session database state and reject duplicate submission attempts with an appropriate error message.
3. Ensure that discount calculations verify maximum usage limits before recalculating order totals.
